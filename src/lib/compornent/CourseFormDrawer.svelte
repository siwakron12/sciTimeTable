<script>
  import { openCourseForm } from "$lib/stores/courseForm";
  import { fly, fade } from "svelte/transition";
  import  Form  from "./Form.svelte"

  const close = () => openCourseForm.set(false);
</script>

{#if $openCourseForm}
  <!-- Background -->
  <div
    class="fixed inset-0 bg-black/40 backdrop-blur-sm z-40"
    on:click={close}
    transition:fade
  ></div>

  <!-- Popup -->
  <div
    class="fixed top-1/2 left-1/2
           w-[90%] max-w-[1000px] max-h-[90vh]
           bg-white shadow-2xl z-50 p-6 md:p-8 rounded-xl
           -translate-x-1/2 -translate-y-1/2
           overflow-auto"
    transition:fly={{ x: 400, duration: 260 }}
    class:-translate-x-full={!$openCourseForm}
  >
    <div class="min-w-[800px]">
      <!-- Header -->
      <div class="flex justify-between items-center mb-6">
        <h1 class="text-3xl font-bold">เพิ่ม/แก้ไขหลักสูตร</h1>
        <button
          on:click={close}
          class="text-3xl font-bold px-3 py-1 hover:bg-gray-200 rounded-lg"
        >
          <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" 
               fill="none" stroke="currentColor" stroke-width="2" 
               stroke-linecap="round" stroke-linejoin="round">
            <path d="M5 12h14"/><path d="m12 5 7 7-7 7"/>
          </svg>
        </button>
      </div>
      <Form/>
     
    </div>
  </div>
{/if}
