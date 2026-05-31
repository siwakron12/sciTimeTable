<script lang="ts">
    import { onMount } from "svelte";
    import Table from "$lib/compornent/Table.svelte";
    import { PUBLIC_API_URL } from "$env/static/public";
    import SearchInput from "$lib/compornent/SearchInput.svelte";
    import PostModel from "./PostModel.svelte";
    import EditModel from "./EditModel.svelte";

    let rows: any = [];
    let Build: any = [];
    let Rooms: any = [];
    let page = 1;
    let search = "";
    let loading = false;
    let HaveLoad = true;
    const columns = [
        { key: "name", label: "ชื่อห้อง " },
        { key: "type", label: "ประเภท" },
        { key: "capacity", label: "ความจุ" },
        { key: "building_Name", label: "ตึก" },
        { key: "manage", label: "จัดการ" }, // แสดงชื่อ department
    ];
    // --- โหลดเฉพาะบน client ---
    onMount(() => {
        loadDepartments();
        loadProgram(false);
    });

    async function loadDepartments() {
        try {
            // ตั้ง URL ให้ถูกต้อง (ไม่มี newline)
            const res = await fetch(`${PUBLIC_API_URL}/api/buildings`);
            if (!res.ok) throw new Error(`status ${res.status}`);
            const data = await res.json();
            Build = data;
            mapProgramsWithDepartment();
        } catch (e) {
            console.error("โหลดข้อมูลภาควิชาล้มเหลว:", e);
        }
    }

    async function loadProgram(isLoadMore = false) {
        loading = true;
        try {
            const q = encodeURIComponent(search.trim());
            const url = search
                ? `${PUBLIC_API_URL}/api/rooms/search?q=${q}&limit=20&page=${page}`
                : `${PUBLIC_API_URL}/api/rooms?limit=10&page=${page}`;

            const res = await fetch(url);
            if (!res.ok) throw new Error(`status ${res.status}`);
            const data = await res.json();
            const programsData = data.data ?? data;

            if (isLoadMore) {
                if (!programsData || programsData.length === 0) {
                    HaveLoad = false;
                } else {
                    Rooms = [...Rooms, ...programsData];
                }
            } else {
                Rooms = programsData || [];
                HaveLoad = true;
            }

            mapProgramsWithDepartment();
        } catch (e) {
            console.error("โหลดข้อมูลหลักสูตรล้มเหลว:", e);
        } finally {
            loading = false;
        }
    }

    function mapProgramsWithDepartment() {
        if (!Rooms || !Build) return;
        rows = Rooms.map((prog: any) => {
            const dep = Build.find((d: any) => d.id === prog.building_id);
            return {
                ...prog,
                building_Name: dep ? dep.name_th : "-",
            };
        });
    }

    function loadMore() {
        if (!HaveLoad || loading) return;
        page++;
        loadProgram(true);
    }

    $: if (search !== undefined) {
        page = 1;
        loadProgram(false);
    }
    let openAdd = false;

    function openPopup() {
        openAdd = true;
    }
    function closePopup() {
        openAdd = false;
    }
    let dataEdit: any = null;
    const handleEdit = (row: any) => {
        dataEdit = { ...row };
    };
    async function deleteProgram(id: number) {
        const confirmDelete = confirm("คุณแน่ใจว่าจะลบห้องนี้?");
        if (!confirmDelete) return;

        try {
            const res = await fetch(`${PUBLIC_API_URL}/api/rooms/${id}`, {
                method: "DELETE",
            });

            if (!res.ok) {
                const errText = await res.text();
                throw new Error(errText || "ลบหลักสูตรไม่สำเร็จ");
            }

            alert("ลบหลักสูตรสำเร็จ!");
            loadProgram(); // reload ตาราง
        } catch (err: any) {
            console.error(err);
            alert("เกิดข้อผิดพลาด: " + err.message);
        }
    }
</script>

<h1 class="text-3xl font-semibold mb-4">จัดการห้อง</h1>
{#if dataEdit !== null}
    <EditModel
        data={dataEdit}
        reLoad={loadProgram}
        onClose={() => (dataEdit = null)}
    />
{/if}
{#if openAdd}
    <PostModel onClose={closePopup} reLoad={loadProgram} />
{/if}
<div class="flex items-center space-x-2 mb-4">
    <SearchInput bind:search placeholder="ค้นหาห้อง..." />
    <button
        class="bg-green-500 px-12 py-2 cursor-pointer text-white rounded-2xl whitespace-nowrap"
        on:click={openPopup}
    >
        เพิ่มข้อมูล
    </button>
</div>
{#if loading}
    <div class="flex justify-center py-8">
        <div
            class="animate-spin rounded-full h-10 w-10 border-b-2 border-orange-500"
        ></div>
    </div>
{:else}
    <Table {columns} {rows} onLoadMore={loadMore} {HaveLoad}>
        <div slot="manage" let:row class="flex gap-4 w-full justify-center">
            <button
                on:click={() => handleEdit(row)}
                class="p-2 bg-[#FF7A00] text-white rounded cursor-pointer"
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
                on:click={() => deleteProgram(row.id)}
                class="p-2 bg-[#FF4141] text-white rounded cursor-pointer"
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
