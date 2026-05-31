<script lang="ts">
      import { PUBLIC_API_URL } from "$env/static/public";
  export let data: any; // row ที่จะ edit
  export let onClose: () => void;
  export let reLoad :()=> void;
  let editData = { ...data }; // clone เพื่อแก้ไขใน modal ไม่กระทบ table โดยตรง
  async function submit() {
    let payload =  {
      name_th:editData.name_th,
     name_en:editData.name_en,

    }

    try {
      const res = await fetch(`${PUBLIC_API_URL}/api/departments/${editData.id}`, {
        method: "PUT",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify(payload),
      });

      if (!res.ok) throw new Error("ส่งข้อมูลไม่สำเร็จ");
      reLoad();
      alert("แก้ไข้รายวิชาสำเร็จ!");
      onClose();
    } catch (err: any) {
      console.error(err);
      alert("เกิดข้อผิดพลาด: " + err.message);
    }
  }
</script>

<div on:click={onClose} class="fixed inset-0 bg-black/40 flex items-center justify-center z-50">
  <div  on:click|stopPropagation class="bg-[#FDEFE2]  rounded p-6 w-96 relative">
    <h2 class="text-xl font-semibold mb-4">แก้ไขภาควิชา</h2>

    <label class="block mb-2">ชื่อภาควิชา (TH)</label>
    <input
      type="text"
      class="w-full border px-3 py-2 rounded mb-4"
      bind:value={editData.name_th}
    />

    <label class="block mb-2">ชื่อภาควิชา (EN)</label>
    <input
      type="text"
      class="w-full border px-3 py-2 rounded mb-4"
      bind:value={editData.name_en}
    />

    <div class="flex justify-center gap-4 mt-4">
     <button class="px-3 py-1 bg-orange-500 text-white rounded" on:click={submit}
        >บันทึก</button
      >
      <button class="px-3 py-1 bg-gray-300 rounded" on:click={onClose}
        >ยกเลิก</button
      >
     
    </div>
  </div>
</div>
