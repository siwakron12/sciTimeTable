<script lang="ts">
  import { page } from "$app/stores";

  let openExcelMenu = false; // toggle menu
  // เปิดเมนูอัตโนมัติถ้าอยู่ใน /manage/*
  $: if ($page.url.pathname.startsWith("/manage")) {
    openExcelMenu = true;
  }

  // ปิดเมนูถ้าออกจาก manage
  $: if (!$page.url.pathname.startsWith("/manage")) {
    openExcelMenu = false;
  }
  const menu = [
    {
      name: "หน้าหลัก",
      path: "/",
      icon: `<svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="lucide lucide-house-icon lucide-house"><path d="M15 21v-8a1 1 0 0 0-1-1h-4a1 1 0 0 0-1 1v8"/><path d="M3 10a2 2 0 0 1 .709-1.528l7-6a2 2 0 0 1 2.582 0l7 6A2 2 0 0 1 21 10v9a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2z"/></svg>`,
    },
    {
      name: "จัดตารางเรียน",
      path: "/mannageTable",
      icon: `<svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="lucide lucide-square-pen-icon lucide-square-pen"><path d="M12 3H5a2 2 0 0 0-2 2v14a2 2 0 0 0 2 2h14a2 2 0 0 0 2-2v-7"/><path d="M18.375 2.625a1 1 0 0 1 3 3l-9.013 9.014a2 2 0 0 1-.853.505l-2.873.84a.5.5 0 0 1-.62-.62l.84-2.873a2 2 0 0 1 .506-.852z"/></svg>`,
    },
    {
      name: "ตารางเรียนทั้งหมด",
      path: "/TableAll",
      icon: `<svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="lucide lucide-grid2x2-icon lucide-grid-2x2"><path d="M12 3v18"/><path d="M3 12h18"/><rect x="3" y="3" width="18" height="18" rx="2"/></svg>`,
    },
    {
      name: "จัดการรายวิชาปัจจุบัน",
      path: "/courseCurrent",
      icon: `<svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="lucide lucide-square-pen-icon lucide-square-pen"><path d="M12 3H5a2 2 0 0 0-2 2v14a2 2 0 0 0 2 2h14a2 2 0 0 0 2-2v-7"/><path d="M18.375 2.625a1 1 0 0 1 3 3l-9.013 9.014a2 2 0 0 1-.853.505l-2.873.84a.5.5 0 0 1-.62-.62l.84-2.873a2 2 0 0 1 .506-.852z"/></svg>`,
    },
    {
      name: "รายวิชาทั้งหมด",
      path: "/courseAll",
      icon: `<svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="lucide lucide-book-open-text-icon lucide-book-open-text"><path d="M12 7v14"/><path d="M16 12h2"/><path d="M16 8h2"/><path d="M3 18a1 1 0 0 1-1-1V4a1 1 0 0 1 1-1h5a4 4 0 0 1 4 4 4 4 0 0 1 4-4h5a1 1 0 0 1 1 1v13a1 1 0 0 1-1 1h-6a3 3 0 0 0-3 3 3 3 0 0 0-3-3z"/><path d="M6 12h2"/><path d="M6 8h2"/></svg>`,
    },
    {
      name: "จัดการข้อมูล",
      path: "/manage",
      icon: `<svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="lucide lucide-folder-pen-icon lucide-folder-pen">
      <path d="M2 11.5V5a2 2 0 0 1 2-2h3.9c.7 0 1.3.3 1.7.9l.8 1.2c.4.6 1 .9 1.7.9H20a2 2 0 0 1 2 2v10a2 2 0 0 1-2 2h-9.5"/><path d="M11.378 13.626a1 1 0 1 0-3.004-3.004l-5.01 5.012a2 2 0 0 0-.506.854l-.837 2.87a.5.5 0 0 0 .62.62l2.87-.837a2 2 0 0 0 .854-.506z"/>
      </svg>`,
      children: [
        { name: "- จัดการภาควิชา", path: "/manage/department" },
        { name: "- จัดการหลักสูตร", path: "/manage/course" },
       
        { name: "- จัดการอื่นๆ", path: "/manage/another" },
      ],
    },
  ];
</script>

<aside 
  class="min-w-60 h-full bg-white border-r justify-between border-gray-100 shadow-lg p-4 flex flex-col gap-2"
>
  <div>
    {#each menu as item}
      {#if item.children}
        <div>
          <!-- ปุ่มเปิด/ปิดเมนูย่อย -->
          <button
            on:click={() => (openExcelMenu = !openExcelMenu)}
            class={`w-full flex items-center justify-between p-3 rounded-lg transition
            ${
              $page.url.pathname.startsWith(item.path)
                ? "bg-[#FF7A00] text-white"
                : "hover:bg-orange-100 text-black"
            }`}
          >
            <div class="flex items-center gap-2">
              {@html item.icon}
              <span>{item.name}</span>
            </div>

            <svg
              class={`transition-transform ${openExcelMenu ? "rotate-90" : ""}`}
              xmlns="http://www.w3.org/2000/svg"
              width="20"
              height="20"
              fill="none"
              stroke="currentColor"
              stroke-width="2"
              stroke-linecap="round"
              stroke-linejoin="round"
            >
              <path d="M6 9l6 6 6-6" />
            </svg>
          </button>

          {#if openExcelMenu}
            <div class="ml-10 mt-1 flex flex-col gap-1">
              {#each item.children as sub}
                <a
                  href={sub.path}
                  on:click={() => (openExcelMenu = true)}
                  class={`p-2 rounded transition
                  ${
                    $page.url.pathname === sub.path
                      ? "text-[#FF7A00] font-semibold"
                      : "text-gray-700 hover:text-[#FF7A00]"
                  }`}
                >
                  {sub.name}
                </a>
              {/each}
            </div>
          {/if}
        </div>
      {:else}
        <a
          href={item.path}
          on:click={() => (openExcelMenu = false)}
          class={`flex items-center gap-2 p-3 rounded-lg transition
          ${
            $page.url.pathname === item.path
              ? "bg-[#FF7A00] text-white"
              : "hover:bg-orange-100 text-black"
          }`}
        >
          {@html item.icon}
          <span>{item.name}</span>
        </a>
      {/if}
    {/each}
  </div>

  <button
    class="gap-2 p-3 rounded-lg border-t border-gray-200 pt-4 flex items-center hover:text-white hover:bg-red-500 cursor-pointer transition-all space-x-2 mt-4"
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
      class="lucide lucide-log-out-icon lucide-log-out"
      ><path d="m16 17 5-5-5-5" /><path d="M21 12H9" /><path
        d="M9 21H5a2 2 0 0 1-2-2V5a2 2 0 0 1 2-2h4"
      /></svg
    >
    <p>ออกจากระบบ</p>
  </button>
</aside>
