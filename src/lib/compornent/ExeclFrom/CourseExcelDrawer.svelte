<script>
  import { openCourseExcelForm } from "$lib/stores/courseExcelForm";
  import { fly, fade } from "svelte/transition";
  import ExcelForm from "./ExcelForm.svelte";

  const close = () => openCourseExcelForm.set(false);
</script>

{#if $openCourseExcelForm}
  <!-- Background Backdrop -->
  <div
    class="fixed inset-0 bg-black/40 backdrop-blur-sm z-40"
    on:click={close}
    transition:fade
  ></div>

  <!-- Popup Box (507 * 507 px) -->
  <div
    class="fixed top-1/2 left-1/2 z-50
           -translate-x-1/2 -translate-y-1/2
           focus:outline-none"
    transition:fly={{ y: 30, duration: 250 }}
  >
    <ExcelForm />
  </div>
{/if}
