<script lang="ts">
    import { onMount } from "svelte";
    import { PUBLIC_API_URL } from "$env/static/public";
    import Table from "$lib/compornent/Table.svelte";
    import { openCourseExcelForm } from "$lib/stores/courseExcelForm";
    import { openCourseForm } from "$lib/stores/courseForm";

    type ScheduleRow = Record<string, any>;

    let schedules: ScheduleRow[] = [];
    let rows: ScheduleRow[] = [];
    let loading = false;
    let search = "";
    let selectedYear = "";
    let selectedDepartment = "";
    let selectedStatus = "";
    let dataEdit: ScheduleRow | null = null;
    let HaveLoad = false;
    const yearOptions = ["1", "2", "3", "4"];
    const statusOptions = ["บันทึกสำเร็จแล้ว", "รอดำเนินการ"];
    let departmentOptions: string[] = [];

    const columns = [
        { key: "courseCode", label: "รหัสวิชา" },
        { key: "courseName", label: "ชื่อรายวิชา" },
        { key: "subjectType", label: "ประเภท" },
        { key: "groupText", label: "กลุ่มเรียน" },
        { key: "studyTime", label: "วัน-เวลาเรียน" },
        { key: "roomText", label: "ห้องเรียน" },
        { key: "buildingText", label: "อาคารเรียน" },
        { key: "statusText", label: "สถานะ" },
        { key: "manage", label: "จัดการ" },
    ];

    const getValue = (row: ScheduleRow, keys: string[], fallback = "-") => {
        for (const key of keys) {
            let value: any = row;
            for (const part of key.split(".")) {
                value = value?.[part];
            }
            if (value !== undefined && value !== null && value !== "") return value;
        }
        return fallback;
    };

    const formatTime = (value: any) => {
        if (!value) return "";
        return String(value).slice(0, 5);
    };

    const createMap = (items: ScheduleRow[], key = "id") => {
        return new Map(items.map((item) => [String(item[key]), item]));
    };

    const fetchJson = async (path: string) => {
        const res = await fetch(`${PUBLIC_API_URL}${path}`);
        if (!res.ok) throw new Error(`${path} status ${res.status}`);
        const data = await res.json();
        return data.data ?? data;
    };

    const normalizeSchedule = (
        row: ScheduleRow,
        refs: {
            courses?: Map<string, ScheduleRow>;
            programs?: Map<string, ScheduleRow>;
            departments?: Map<string, ScheduleRow>;
            rooms?: Map<string, ScheduleRow>;
            buildings?: Map<string, ScheduleRow>;
        } = {},
    ) => {
        const course = refs.courses?.get(String(row.course_id));
        const program = refs.programs?.get(String(row.program_id));
        const department = refs.departments?.get(String(program?.department_id));
        const room = refs.rooms?.get(String(row.room_id));
        const building = refs.buildings?.get(String(room?.building_id));
        const day = getValue(row, ["day", "study_day"], "");
        const startTime = formatTime(getValue(row, ["start_time", "startTime"], ""));
        const endTime = formatTime(getValue(row, ["end_time", "endTime"], ""));
        const timeText = day && startTime && endTime ? `${day}.${startTime}-${endTime}` : "-";
        const courseName = getValue(course ?? {}, ["name_en", "name_th"], "");
        const roomName = getValue(room ?? {}, ["name"], "");
        const buildingName = getValue(building ?? {}, ["name_th", "name_en"], "");
        const departmentName = getValue(department ?? {}, ["name_th", "name_en"], "");

        return {
            ...row,
            course,
            program,
            department,
            room,
            building,
            courseCode: getValue(row, ["course_id", "course.code", "course.course_id", "course_code"]),
            courseName: courseName || getValue(row, ["course.name_en", "name_en", "course_name", "course.name_th", "name_th"]),
            subjectType: getValue(row, ["type", "subject_type"]),
            groupText: getValue(row, ["group", "group_no", "section"]),
            studyTime: timeText,
            roomText: roomName || getValue(row, ["room.name", "room.room", "room", "room_name", "room_id"]),
            buildingText: buildingName || getValue(row, ["building.name", "building", "building_name"]),
            statusText: getValue(row, ["status"], "บันทึกสำเร็จแล้ว"),
            departmentText: departmentName || getValue(row, [
                    "department.name_th",
                    "course.department.name_th",
                    "program.department.name_th",
                    "department",
                ], ""),
            yearText: String(getValue(row, ["year", "year_level", "student_year", "course.year"], "")),
        };
    };

    async function loadTables() {
        loading = true;
        try {
            const [
                schedulesData,
                coursesData,
                programsData,
                departmentsData,
                roomsData,
                buildingsData,
            ] = await Promise.all([
                fetchJson("/api/schedules"),
                fetchJson("/api/courses"),
                fetchJson("/api/programs"),
                fetchJson("/api/departments"),
                fetchJson("/api/rooms"),
                fetchJson("/api/buildings"),
            ]);

            const refs = {
                courses: createMap(Array.isArray(coursesData) ? coursesData : []),
                programs: createMap(Array.isArray(programsData) ? programsData : []),
                departments: createMap(Array.isArray(departmentsData) ? departmentsData : []),
                rooms: createMap(Array.isArray(roomsData) ? roomsData : []),
                buildings: createMap(Array.isArray(buildingsData) ? buildingsData : []),
            };

            departmentOptions = Array.isArray(departmentsData)
                ? departmentsData
                      .map((department) => getValue(department, ["name_th", "name_en"], ""))
                      .filter((department) => department && department !== "-")
                : [];

            schedules = Array.isArray(schedulesData)
                ? schedulesData.map((schedule) => normalizeSchedule(schedule, refs))
                : [];
        } catch (e) {
            console.error("โหลดข้อมูลตารางล้มเหลว:", e);
            schedules = [];
        } finally {
            loading = false;
        }
    }

    $: rows = schedules.filter((row) => {
        const keyword = search.trim().toLowerCase();
        const matchSearch =
            !keyword ||
            [
                row.courseCode,
                row.courseName,
                row.subjectType,
                row.groupText,
                row.studyTime,
                row.roomText,
                row.buildingText,
                row.statusText,
            ]
                .join(" ")
                .toLowerCase()
                .includes(keyword);

        const matchYear = !selectedYear || !row.yearText || row.yearText === selectedYear;
        const matchDepartment = !selectedDepartment || row.departmentText === selectedDepartment;
        const matchStatus = !selectedStatus || row.statusText === selectedStatus;

        return matchSearch && matchYear && matchDepartment && matchStatus;
    });

    onMount(() => {
        loadTables();
    });

    const clearSearch = () => {
        search = "";
    };

    const loadMore = () => {};

    const handleEdit = (row: ScheduleRow) => {
        dataEdit = { ...row };
    };

    const closeModal = () => {
        dataEdit = null;
    };

    const handleDelete = async (row: ScheduleRow) => {
        const name = row.courseName || row.course_id || row.id;
        const confirmDelete = window.confirm(`คุณต้องการลบ "${name}" ใช่หรือไม่?`);
        if (!confirmDelete) return;

        try {
            const res = await fetch(`${PUBLIC_API_URL}/api/schedules/${row.id}`, {
                method: "DELETE",
            });
            if (!res.ok) throw new Error("ลบข้อมูลไม่สำเร็จ");
            await loadTables();
            alert("ลบสำเร็จ");
        } catch (err: any) {
            console.error(err);
            alert("เกิดข้อผิดพลาด: " + err.message);
        }
    };
</script>

<div class="w-full">
    <div class="mb-6 flex flex-wrap items-center justify-between gap-4">
        <h1 class="text-3xl font-semibold">จัดตารางเรียน</h1>

        <div class="flex flex-wrap items-center gap-4">
            <button
                class="flex items-center gap-2 rounded-xl bg-[#1D6F42] px-5 py-2 text-sm font-medium text-white"
                on:click={() => openCourseExcelForm.set(true)}
            >
                <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                    <path d="M12 3v17a1 1 0 0 1-1 1H5a2 2 0 0 1-2-2V5a2 2 0 0 1 2-2h14a2 2 0 0 1 2 2v6a1 1 0 0 1-1 1H3" />
                    <path d="M16 19h6" />
                    <path d="M19 22v-6" />
                </svg>
                เพิ่มด้วย Excel
            </button>

            <button
                class="flex items-center gap-2 rounded-xl bg-[#FF7900] px-5 py-2 text-sm font-medium text-white"
                on:click={() => openCourseForm.set(true)}
            >
                <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                    <circle cx="12" cy="12" r="10" />
                    <path d="M8 12h8" />
                    <path d="M12 8v8" />
                </svg>
                เพิ่มข้อมูล
            </button>

            <button class="flex items-center gap-2 rounded-xl bg-[#FF7900] px-5 py-2 text-sm font-medium text-white">
                <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                    <path d="m22 2-7 20-4-9-9-4Z" />
                    <path d="M22 2 11 13" />
                </svg>
                ส่งตารางเรียน
            </button>
        </div>
    </div>

    <div class="mb-5 flex items-center gap-2">
        <div class="flex min-w-[280px] flex-1 items-center rounded-xl border border-gray-300 bg-gray-100 px-4 py-2">
            <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                <path d="m21 21-4.34-4.34" />
                <circle cx="11" cy="11" r="8" />
            </svg>
            <input
                type="text"
                placeholder="ค้นหารายวิชา"
                bind:value={search}
                class="w-full bg-transparent px-4 py-1 outline-none"
            />
        </div>

        <button class="rounded-xl bg-black px-10 py-3 text-sm font-semibold text-white" on:click={clearSearch}>
            ล้าง
        </button>
    </div>

    <div class="mb-8 grid gap-4 md:grid-cols-3">
        <label class="flex items-center gap-4">
            <span class="whitespace-nowrap text-lg font-semibold">ชั้นปีที่</span>
            <select bind:value={selectedYear} class="min-w-0 flex-1 rounded-xl border border-gray-300 bg-gray-100 px-4 py-2.5 text-base outline-none">
                <option value="">ทั้งหมด</option>
                {#each yearOptions as year}
                    <option value={year}>{year}</option>
                {/each}
            </select>
        </label>

        <label class="flex items-center gap-4">
            <span class="whitespace-nowrap text-lg font-semibold">ภาควิชา</span>
            <select bind:value={selectedDepartment} class="min-w-0 flex-1 rounded-xl border border-gray-300 bg-gray-100 px-4 py-2.5 text-base outline-none">
                <option value="">ทั้งหมด</option>
                {#each departmentOptions as department}
                    <option value={department}>{department}</option>
                {/each}
            </select>
        </label>

        <label class="flex items-center gap-4">
            <span class="whitespace-nowrap text-lg font-semibold">สถานะ</span>
            <select bind:value={selectedStatus} class="min-w-0 flex-1 rounded-xl border border-gray-300 bg-gray-100 px-4 py-2.5 text-base outline-none">
                <option value="">ทั้งหมด</option>
                {#each statusOptions as status}
                    <option value={status}>{status}</option>
                {/each}
            </select>
        </label>
    </div>

    {#if dataEdit !== null}
        <div class="fixed inset-0 z-50 flex items-center justify-center">
            <button
                type="button"
                aria-label="ปิดหน้าต่างแก้ไข"
                class="absolute inset-0 bg-black/40"
                on:click={closeModal}
            ></button>
            <div class="relative w-full max-w-lg rounded-2xl bg-white p-8 shadow-xl">
                <h2 class="mb-4 text-2xl font-semibold">แก้ไขตารางเรียน</h2>
                <p class="mb-6 text-gray-600">ฟอร์มแก้ไขตารางเรียนยังไม่ได้เชื่อมต่อในหน้านี้</p>
                <button class="rounded-xl bg-[#FF7900] px-6 py-2 text-white" on:click={closeModal}>ปิด</button>
            </div>
        </div>
    {/if}

    {#if loading}
        <div class="flex justify-center py-8">
            <div class="h-10 w-10 animate-spin rounded-full border-b-2 border-orange-500"></div>
        </div>
    {:else if rows.length === 0}
        <div class="rounded-xl border border-orange-100 bg-orange-50 py-10 text-center text-orange-500">
            ไม่พบข้อมูลตารางเรียน
        </div>
    {:else}
        <Table {columns} {rows} onLoadMore={loadMore} {HaveLoad}>
            <div slot="manage" let:row class="flex gap-4 w-full justify-center">
                <button
                    class="p-2 bg-[#FF7A00] text-white rounded cursor-pointer"
                    aria-label="แก้ไข"
                    on:click={() => handleEdit(row)}
                >
                    <svg
                        xmlns="http://www.w3.org/2000/svg"
                        width="24"
                        height="24"
                        viewBox="0 0 24 24"
                        fill="none"
                        stroke="currentColor"
                        stroke-width="2"
                        stroke-linecap="round"
                        stroke-linejoin="round"
                        class="lucide lucide-pencil-icon lucide-pencil"
                        ><path
                            d="M21.174 6.812a1 1 0 0 0-3.986-3.987L3.842 16.174a2 2 0 0 0-.5.83l-1.321 4.352a.5.5 0 0 0 .623.622l4.353-1.32a2 2 0 0 0 .83-.497z"
                        /><path d="m15 5 4 4" /></svg
                    >
                </button>

                <button
                    class="p-2 bg-[#FF4141] text-white rounded cursor-pointer"
                    aria-label="ลบ"
                    on:click={() => handleDelete(row)}
                >
                    <svg
                        xmlns="http://www.w3.org/2000/svg"
                        width="24"
                        height="24"
                        viewBox="0 0 24 24"
                        fill="none"
                        stroke="currentColor"
                        stroke-width="2"
                        stroke-linecap="round"
                        stroke-linejoin="round"
                        class="lucide lucide-trash2-icon lucide-trash-2"
                        ><path d="M10 11v6" /><path d="M14 11v6" /><path
                            d="M19 6v14a2 2 0 0 1-2 2H7a2 2 0 0 1-2-2V6"
                        /><path d="M3 6h18" /><path
                            d="M8 6V4a2 2 0 0 1 2-2h4a2 2 0 0 1 2 2v2"
                        /></svg
                    >
                </button>
            </div>
        </Table>
    {/if}
</div>
