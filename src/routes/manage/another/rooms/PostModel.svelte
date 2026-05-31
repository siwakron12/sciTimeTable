<script lang="ts">
    import { PUBLIC_API_URL } from "$env/static/public";

    export let onClose = () => {};
    export let reLoad = () => {};

    // ฟิลด์ทั้งหมด
    let name_th = "";
    let capacity = "";
    let department_id = "";
    let type = "";

    let Depart: any[] = []; // เก็บข้อมูลภาควิชา
    let roomType: any[] = [];
    // โหลดภาควิชา
    async function loadDepartments() {
        try {
            const res = await fetch(`${PUBLIC_API_URL}/api/buildings`);
            const data = await res.json();
            Depart = data;
        } catch (err) {
            console.error("โหลดข้อมูลภาควิชาล้มเหลว:", err);
        }
    }
    loadRoomType();
    async function loadRoomType() {
        try {
            const res = await fetch(`${PUBLIC_API_URL}/api/room_types`);
            const data = await res.json();
            roomType = data;
        } catch (err) {
            console.error("โหลดข้อมูลภาควิชาล้มเหลว:", err);
        }
    }

    loadDepartments();

    const resetForm = () => {
        name_th = "";
        capacity = "";
        department_id = "";
        type = "";
    };

    async function submit() {
        if (!name_th || !capacity || !department_id || !type) {
            alert("กรุณากรอกข้อมูลให้ครบทุกช่อง");
            return;
        }

        const payload = {
            name: name_th,
            capacity: Number(capacity) <= 0 ? 0 : Number(capacity),
            building_id: String(department_id),
            type: type,
        };

        try {
            const res = await fetch(`${PUBLIC_API_URL}/api/rooms`, {
                method: "POST",
                headers: { "Content-Type": "application/json" },
                body: JSON.stringify(payload),
            });

            if (!res.ok) {
                const errText = await res.text();
                throw new Error(errText || "ส่งข้อมูลไม่สำเร็จ");
            }
            reLoad();

            alert("เพิ่มหลักสูตรสำเร็จ!");
            onClose();
        } catch (err: any) {
            console.error(err);
            alert("เกิดข้อผิดพลาด: " + err.message);
        }
    }
  
</script>

<!-- Background คลิกปิด -->
<div class="fixed inset-0 bg-black/40 backdrop-blur-sm z-50" on:click={onClose}>
    <!-- Popup กล่อง -->
    <div class="fixed inset-0 flex items-center justify-center z-50">
        <div
            class="relative p-8 bg-[#FDEFE2] rounded-2xl w-full max-w-lg border border-orange-200 shadow-xl"
            on:click|stopPropagation
        >
            <h2 class="text-2xl font-bold mb-6">เพิ่มหลักสูตร</h2>

            <div class="items-center mb-4 flex w-full">
                <label class="w-[100px]"
                    >ชื่อห้อง <span class="text-red-500">*</span> :</label
                >
                <input
                    type="text"
                    bind:value={name_th}
                    placeholder=""
                    class="flex-1 px-3 py-2 border rounded-md outline-none"
                />
            </div>

            <div class="items-center mb-4 flex w-full">
                <label class="w-[100px]"
                    >ความจุห้อง <span class="text-red-500">*</span> :</label
                >
                <input
                    type="number"
                    min={1}
                    bind:value={capacity}
                    placeholder=""
                    class="flex-1 px-3 py-2 border rounded-md outline-none"
                />
            </div>

            <!-- เลือกภาควิชา -->
            <div class="items-center mb-4 flex w-full">
                <label class="w-[100px] shrink-0">
                    ตึกที่อยู่ <span class="text-red-500">*</span> :
                </label>

                <select
                    bind:value={department_id}
                    class="w-full px-3 py-2 border rounded-md outline-none"
                >
                    <option value="">-- เลือกตึก --</option>
                    {#each Depart as dep}
                        <option value={dep.id}>{dep.name_th}</option>
                    {/each}
                </select>
            </div>

            <div class="items-center mb-4 flex w-full">
                <label class="w-[100px]"
                    >ประเภทห้อง <span class="text-red-500">*</span> :</label
                >
                <select
                    bind:value={type}
                    class="flex-1 px-3 py-2 border rounded-md outline-none"
                >
                    <option value="">-- เลือกประเภท --</option>
                    {#each roomType as dep}
                        <option value={dep.name}>{dep.name}</option>
                    {/each}
                </select>
            </div>

            <!-- ปุ่ม -->
            <div class="flex justify-center gap-6">
                <button
                    class="px-10 py-2 bg-orange-500 text-white rounded-lg hover:bg-orange-600"
                    on:click={submit}
                >
                    ยืนยัน
                </button>
                <button
                    class="px-10 py-2 bg-white border rounded-lg hover:bg-gray-200"
                    on:click={onClose}
                >
                    ยกเลิก
                </button>
            </div>
        </div>
    </div>
</div>
