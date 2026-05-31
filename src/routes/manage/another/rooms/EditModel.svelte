<script lang="ts">
    import { PUBLIC_API_URL } from "$env/static/public";

    export let onClose = () => {};
    export let reLoad = () => {};
    export let data: any; // ข้อมูล row ที่ส่งมา edit

    let name_th = data.name;
    let type = data.type;
    let department_id = data.building_id;
    let capacity = data.capacity;

    let Depart: any[] = [];

    async function loadDepartments() {
        try {
            const res = await fetch(`${PUBLIC_API_URL}/api/buildings`);
            Depart = await res.json();
        } catch (err) {
            console.error("โหลดข้อมูลภาควิชาล้มเหลว:", err);
        }
    }
    loadDepartments();
    loadRoomType();
    let roomType: any = [];
    async function loadRoomType() {
        try {
            const res = await fetch(`${PUBLIC_API_URL}/api/room_types`);
            const data = await res.json();
            roomType = data;
        } catch (err) {
            console.error("โหลดข้อมูลภาควิชาล้มเหลว:", err);
        }
    }
    const resetForm = () => {
        name_th = data.name_th;
        type = data.type;
        department_id = data.department_id;
        capacity = data.capacity;
    };

    async function submit() {
        if (!name_th || !type || !department_id || !capacity) {
            alert("กรุณากรอกข้อมูลให้ครบทุกช่อง");
            return;
        }

        const payload = {
            name: name_th,
            type,
            building_id: String(department_id),
            capacity: Number(capacity) <= 0 ? 0 : Number(capacity),
        };
        console.log("🚀 ~ file: EditModel.svelte:93 ~ submit ~ payload:", payload);
        try {
            const res = await fetch(`${PUBLIC_API_URL}/api/rooms/${data.id}`, {
                method: "PUT",
                headers: { "Content-Type": "application/json" },
                body: JSON.stringify(payload),
            });

            if (!res.ok) {
                const errText = await res.text();
                throw new Error(errText || "แก้ไขข้อมูลไม่สำเร็จ");
            }

            alert("แก้ไขข้อมูลสำเร็จ!");
            reLoad();
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
            <h2 class="text-2xl font-bold mb-6">แก้ไขหลักสูตร</h2>
            <!-- <div class="items-center mb-4 flex w-full">
                <label class="w-[100px]"
                    >ชื่อห้อง <span class="text-red-500">*</span> :</label
                >
                <input
                    type="text"
                    bind:value={name_th}
                    placeholder="ระบุชื่อหลักสูตรภาษาไทย"
                    class="flex-1 px-3 py-2 border rounded-md outline-none"
                />
            </div> -->
            <div class="mb-4 flex items-center">
                <label class="w-[100px]"
                    >ชื่อห้อง <span class="text-red-500">*</span> :</label
                >
                <input
                    type="text"
                    bind:value={name_th}
                    class="flex-1 px-3 py-2 border rounded-md outline-none"
                />
            </div>

            <div class="mb-4 flex items-center">
                <label class="w-[100px]"
                    >ตึกที่สังกัด <span class="text-red-500">*</span> :</label
                >
                <select
                    bind:value={department_id}
                    class="flex-1 px-3 py-2 border rounded-md outline-none"
                >
                    <option value="">-- เลือกตึก --</option>
                    {#each Depart as dep}
                        <option value={dep.id}>{dep.name_th}</option>
                    {/each}
                </select>
            </div>
            <div class="mb-4 flex items-center">
                <label class="w-[130px]"
                    >ประเภทห้อง <span class="text-red-500">*</span> :</label
                >
                <select
                    bind:value={type}
                    class="w-full px-3 py-2 border rounded-md outline-none"
                >
                    <option value="">-- เลือกประเภทห้อง --</option>
                    {#each roomType as dep}
                        <option value={dep.name}>{dep.name}</option>
                    {/each}
                </select>
            </div>

            <div class="mb-6 flex items-center">
                <label class="w-[100px]"
                    >ความจุ <span class="text-red-500">*</span> :</label
                >
                <input
                    type="number"
                    bind:value={capacity}
                    min="1"
                    class="w-full px-3 py-2 border rounded-md outline-none"
                />
            </div>

            <div class="flex justify-center gap-6">
                <button
                    class="px-10 py-2 bg-orange-500 text-white rounded-lg hover:bg-orange-600"
                    on:click={submit}
                >
                    บันทึก
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
