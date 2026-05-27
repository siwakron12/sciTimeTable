<script>
  import { openCourseExcelForm } from "$lib/stores/courseExcelForm";
  import { openCourseForm } from "$lib/stores/courseForm";
</script>
<div class="flex space-x-3">
    <button class="py-2 px-4 flex items-center  space-x-2 text-base bg-[#1D6F42] text-white rounded-xl ">
        <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="lucide lucide-download-icon lucide-download"><path d="M12 15V3"/><path d="M21 15v4a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2v-4"/><path d="m7 10 5 5 5-5"/></svg>
        <p>Template Execl</p>
    </button>
    <button on:click={() => openCourseExcelForm.set(true)} class="cursor-pointer py-2 px-4 flex items-center  space-x-2 text-base bg-[#1D6F42] text-white rounded-xl">
        <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="lucide lucide-grid2x2-plus-icon lucide-grid-2x2-plus"><path d="M12 3v17a1 1 0 0 1-1 1H5a2 2 0 0 1-2-2V5a2 2 0 0 1 2-2h14a2 2 0 0 1 2 2v6a1 1 0 0 1-1 1H3"/><path d="M16 19h6"/><path d="M19 22v-6"/></svg>
        <p>เพิ่มด้วย Execl</p>
    </button>
    <button on:click={() => openCourseForm.set(true)} class="cursor-pointer py-2 px-4 flex items-center  space-x-2 text-base bg-[#FF7A00] text-white rounded-xl">
        <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="lucide lucide-circle-plus-icon lucide-circle-plus"><circle cx="12" cy="12" r="10"/><path d="M8 12h8"/><path d="M12 8v8"/></svg>
        <p>เพื่มหลักสูตร</p>
    </button>

</div>