<script>
  import { Eraser } from "@lucide/svelte";

    export let search = "";
    export let placeholder = "ค้นหา...";
</script>

<div class="flex  w-full items-center h-full  gap-2">
    <div class="flex items-center w-full px-4 py-2 border border-gray-600 bg-gray-100 rounded-3xl">
        <svg
            xmlns="http://www.w3.org/2000/svg"
            width="24"
            height="24"
            fill="none"
            stroke="currentColor"
            stroke-width="2"
            stroke-linecap="round"
            stroke-linejoin="round"
            class="lucide lucide-search"
        >
            <path d="m21 21-4.34-4.34" /><circle cx="11" cy="11" r="8" />
        </svg>

        <input
            type="text"
            {placeholder}
            bind:value={search}
            class="w-full bg-transparent outline-none p-2"
        />
    </div>
 
 <div> 
    
        <button
        class="px-12 flex items-center   py-2 bg-black text-white rounded-2xl cursor-pointer"
        on:click={() => (search = "")}
    >
        <Eraser size={20} />
        <span class="ml-2">ล้าง</span>
     
    </button>
 </div>     

</div>
