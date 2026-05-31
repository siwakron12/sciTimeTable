<script lang="ts">
  type ReadTableRow = {
    course_id?: string;
    name_th?: string;
    name_en?: string;
    faculty?: string;
    program_name?: string;
    departments_name?: string;
    day?: string;
    start_time?: string;
    end_time?: string;
    final_day?: string;
    final_day_name?: string;
    final_day_number?: string;
    final_start_time?: string;
    final_end_time?: string;
    mid_day?: string;
    mid_day_name?: string;
    mid_day_number?: string;
    mid_start_time?: string;
    mid_end_time?: string;
    group?: number | string;
    pair_group?: number | string;
    lecturer?: string;
    room_id?: string;
    room_name?: string;
    building_name?: string;
    building_number?: string;
    program_id?: number | string;
    student_count?: number | string;
    type?: string;
    credit?: number | string;
    lps?: string;
    academic_year?: string;
    semester?: number | string;
    problem?: string[];
  };

  export let rows: ReadTableRow[] = [];
  console.log("🚀 ~ file: CourseExcelPreview.svelte:24 ~ rows:", rows);
  export let onClose: () => void;
  export let onBack: () => void;

  type ProblemFilter =
    | "all"
    | "problem"
    | "no-problem"
    | "problem-1"
    | "problem-2"
    | "overlap";

  let selectedRowIndexes: number[] = [];
  let selectedProblemFilter: ProblemFilter = "all";
  let isProblemFilterOpen = false;
  let isLegendPopupOpen = false;

  const getProblemCount = (row: ReadTableRow) =>
    Array.isArray(row.problem) ? row.problem.length : 0;

  const hasOverlapProblem = (row: ReadTableRow) =>
    Array.isArray(row.problem) &&
    row.problem.some((problem) => String(problem).includes("ทับ"));

  const getProblemFilterLabel = (filter: ProblemFilter) => {
    if (filter === "problem") {
      return "ข้อมูลทั้งหมดที่มีปัญหา";
    }

    if (filter === "no-problem") {
      return "ข้อมูลที่ไม่มีปัญหา";
    }

    if (filter === "problem-1") {
      return "ข้อมูลที่มีปัญหา 1";
    }

    if (filter === "problem-2") {
      return "ข้อมูลที่มีปัญหา 2";
    }

    return "ข้อมูลทั้งหมด";
  };

  const problemFilterOptions = [
    { value: "all", label: "ข้อมูลทั้งหมด" },
    { value: "problem", label: "ข้อมูลทั้งหมดที่มีปัญหา" },
    { value: "no-problem", label: "ข้อมูลที่ไม่มีปัญหา" },
    { value: "problem-1", label: "ข้อมูลที่มีปัญหา 1" },
    { value: "problem-2", label: "ข้อมูลที่มีปัญหา 2" },
    { value: "overlap", label: "ข้อมูลที่ไม่สามารถทับได้" },
  ] as const;

  const formatTimeRange = (row: ReadTableRow) => {
    const startTime = row.start_time ? String(row.start_time).slice(0, 5) : "-";
    const endTime = row.end_time ? String(row.end_time).slice(0, 5) : "-";
    const day = row.day || "-";
    return `${day} ${startTime}-${endTime}`.trim();
  };

  const formatExamRange = (
    dayName?: string,
    dayNumber?: string,
    startTime?: string,
    endTime?: string,
  ) => {
    const formattedStartTime = startTime ? String(startTime).slice(0, 5) : "-";
    const formattedEndTime = endTime ? String(endTime).slice(0, 5) : "-";
    const formattedDayName = dayName || "-";
    const formattedDayNumber = dayNumber || "-";

    return `${formattedDayName} (${formattedDayNumber}) ${formattedStartTime}-${formattedEndTime}`;
  };

  const formatProblems = (row: ReadTableRow) => {
    if (!Array.isArray(row.problem) || row.problem.length === 0) {
      return "-";
    }

    return row.problem.join(", ");
  };

  const matchesProblemFilter = (row: ReadTableRow, filter: ProblemFilter) => {
    const problemCount = getProblemCount(row);

    if (filter === "overlap") {
      return hasOverlapProblem(row);
    }

    if (filter === "problem") {
      return problemCount > 0;
    }

    if (filter === "no-problem") {
      return problemCount === 0;
    }

    if (filter === "problem-1") {
      return problemCount === 1;
    }

    if (filter === "problem-2") {
      return problemCount >= 2;
    }

    return true;
  };

  const selectProblemFilter = (filter: ProblemFilter) => {
    selectedProblemFilter = filter;
    isProblemFilterOpen = false;
  };

  $: filteredRows = rows
    .map((row, index) => ({ row, index }))
    .filter(({ row }) => matchesProblemFilter(row, selectedProblemFilter));

  $: allVisibleRowsSelected =
    filteredRows.length > 0 &&
    filteredRows.every(({ index }) => selectedRowIndexes.includes(index));

  const getRowTone = (row: ReadTableRow) => {
    const problemCount = getProblemCount(row);

    if (problemCount >= 2) {
      return "danger";
    }

    if (problemCount === 1) {
      return "warning";
    }

    return "normal";
  };

  const toggleRowSelection = (index: number) => {
    if (selectedRowIndexes.includes(index)) {
      selectedRowIndexes = selectedRowIndexes.filter(
        (selectedIndex) => selectedIndex !== index,
      );
      return;
    }

    selectedRowIndexes = [...selectedRowIndexes, index];
  };

  const toggleAllRows = () => {
    const visibleIndexes = filteredRows.map(({ index }) => index);

    if (
      visibleIndexes.length > 0 &&
      visibleIndexes.every((index) => selectedRowIndexes.includes(index))
    ) {
      selectedRowIndexes = [];
      return;
    }

    selectedRowIndexes = Array.from(
      new Set([...selectedRowIndexes, ...visibleIndexes]),
    );
  };

  const problemRowCount = () =>
    rows.filter((row) => getRowTone(row) !== "normal").length;
  const visibleProblemRowCount = () =>
    filteredRows.filter(({ row }) => getRowTone(row) !== "normal").length;

  const dangerRowCount = () =>
    rows.filter((row) => getRowTone(row) === "danger").length;
  const warningRowCount = () =>
    rows.filter((row) => getRowTone(row) === "warning").length;
  const normalRowCount = () =>
    rows.filter((row) => getRowTone(row) === "normal").length;
</script>

<div class="overlay" style="z-index:80;">
  <button
    type="button"
    class="backdrop absolute inset-0 backdrop-blur-sm"
    aria-label="ปิดหน้าต่างจัดการ Excel"
    on:click={onClose}
  ></button>

  <div class="modal">
    <div class="flex items-start justify-between gap-4">
      <div>
        <h2 class="text-[32px] font-bold text-[#FD6F02] leading-tight">
          จัดการ Excel
        </h2>
        <p class="mt-1 text-sm text-gray-600">พบข้อมูล {rows.length}</p>
      </div>

      <button
        type="button"
        on:click={onClose}
        class="rounded-full p-2 text-gray-500 transition-colors hover:bg-black/5 hover:text-black"
        aria-label="ปิด"
      >
        <svg
          xmlns="http://www.w3.org/2000/svg"
          width="30"
          height="30"
          viewBox="0 0 24 24"
          fill="none"
          stroke="currentColor"
          stroke-width="2.2"
          stroke-linecap="round"
          stroke-linejoin="round"
        >
          <line x1="18" y1="6" x2="6" y2="18"></line>
          <line x1="6" y1="6" x2="18" y2="18"></line>
        </svg>
      </button>
    </div>
    <div
      class="relative flex flex-col gap-4 xl:flex-row xl:items-start xl:justify-between"
    >
      <div class="w-full max-w-[360px]">
        <div class="relative w-full">
          <button
            type="button"
            class="flex w-full items-center justify-between rounded-2xl bg-[#FD8B1A] px-4 py-3 text-left text-white shadow-sm transition-colors hover:bg-[#f37f04]"
            on:click={() => (isProblemFilterOpen = !isProblemFilterOpen)}
            aria-expanded={isProblemFilterOpen}
            aria-haspopup="listbox"
          >
            <span class="flex items-center gap-3 font-semibold">
              <svg
                xmlns="http://www.w3.org/2000/svg"
                width="18"
                height="18"
                viewBox="0 0 24 24"
                fill="none"
                stroke="currentColor"
                stroke-width="2.2"
                stroke-linecap="round"
                stroke-linejoin="round"
              >
                <circle cx="12" cy="12" r="4"></circle>
                <path
                  d="M2 12c2.5-4.5 6.5-7 10-7s7.5 2.5 10 7c-2.5 4.5-6.5 7-10 7s-7.5-2.5-10-7z"
                ></path>
              </svg>
              <span>แสดงข้อมูล</span>
            </span>
            <svg
              xmlns="http://www.w3.org/2000/svg"
              width="18"
              height="18"
              viewBox="0 0 24 24"
              fill="none"
              stroke="currentColor"
              stroke-width="2.4"
              stroke-linecap="round"
              stroke-linejoin="round"
              class:rotate-180={isProblemFilterOpen}
            >
              <polyline points="6 9 12 15 18 9"></polyline>
            </svg>
          </button>

          {#if isProblemFilterOpen}
            <div
              class="absolute z-20 mt-2 w-full overflow-hidden rounded-2xl border border-[#FD6F02]/20 bg-white shadow-xl"
            >
              {#each problemFilterOptions as option}
                <button
                  type="button"
                  class:selected-filter={selectedProblemFilter === option.value}
                  class="flex w-full items-start gap-3 border-b border-gray-100 px-4 py-3 text-left text-sm text-gray-800 transition-colors last:border-b-0 hover:bg-[#FFF5E8]"
                  on:click={() => selectProblemFilter(option.value)}
                >
                  <span class="mt-1 text-lg leading-none text-gray-700">•</span>
                  <span class="font-medium">{option.label}</span>
                </button>
              {/each}
            </div>
          {/if}
        </div>
      </div>

      {#if isLegendPopupOpen}
        <div
          class="absolute right-0 top-14 z-30 w-full max-w-[420px] rounded-3xl border border-[#FD6F02]/20 bg-white p-4 shadow-2xl xl:right-4"
        >
          <div class="flex items-start justify-between gap-3">
            <div>
              <h4 class="text-base font-bold text-gray-900">คำอธิบายสี</h4>
              <p class="text-sm text-gray-500">อธิบายระดับปัญหาของแต่ละแถว</p>
            </div>
            <button
              type="button"
              class="rounded-full p-1 text-gray-500 transition-colors hover:bg-black/5 hover:text-black"
              on:click={() => (isLegendPopupOpen = false)}
              aria-label="ปิดคำอธิบายสี"
            >
              <svg
                xmlns="http://www.w3.org/2000/svg"
                width="20"
                height="20"
                viewBox="0 0 24 24"
                fill="none"
                stroke="currentColor"
                stroke-width="2.2"
                stroke-linecap="round"
                stroke-linejoin="round"
              >
                <line x1="18" y1="6" x2="6" y2="18"></line>
                <line x1="6" y1="6" x2="18" y2="18"></line>
              </svg>
            </button>
          </div>

          <div class="mt-4 space-y-3">
            <div
              class="flex items-start gap-3 rounded-2xl bg-rose-50 px-3 py-3"
            >
              <span class="mt-1 h-4 w-4 shrink-0 rounded-full bg-rose-400"
              ></span>
              <div>
                <div class="font-semibold text-gray-900">
                  ข้อมูลที่มีปัญหาหนัก
                </div>
                <div class="text-sm text-gray-600">
                  แถวที่มีปัญหาหลายจุด ต้องตรวจสอบก่อนใช้งาน
                </div>
              </div>
            </div>
            <div
              class="flex items-start gap-3 rounded-2xl bg-amber-50 px-3 py-3"
            >
              <span class="mt-1 h-4 w-4 shrink-0 rounded-full bg-amber-400"
              ></span>
              <div>
                <div class="font-semibold text-gray-900">
                  ข้อมูลที่มีปัญหาปานกลาง
                </div>
                <div class="text-sm text-gray-600">
                  แถวที่มีปัญหาเล็กน้อย ควรทบทวนรายละเอียด
                </div>
              </div>
            </div>
            <div
              class="flex items-start gap-3 rounded-2xl bg-yellow-50 px-3 py-3"
            >
              <span class="mt-1 h-4 w-4 shrink-0 rounded-full bg-yellow-300"
              ></span>
              <div>
                <div class="font-semibold text-gray-900">
                  ข้อมูลที่ต้องยืนยันอีกครั้ง
                </div>
                <div class="text-sm text-gray-600">
                  แถวที่ยังควรเช็กความถูกต้องก่อนบันทึก
                </div>
              </div>
            </div>
          </div>
        </div>
      {/if}
    </div>

    <div class="flex flex-wrap items-center gap-3 text-sm">
      <div class="flex flex-wrap items-center gap-3 text-sm">
        <button
          type="button"
          on:click={toggleAllRows}
          class="rounded-full flex items-center space-x-1.5 border border-[#FD6F02]/30 px-3 py-1.5 font-semibold text-[#FD6F02] transition-colors hover:bg-[#FD6F02]/10"
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
            class="lucide lucide-mouse-pointer-click-icon lucide-mouse-pointer-click"
            ><path d="M14 4.1 12 6" /><path d="m5.1 8-2.9-.8" /><path
              d="m6 12-1.9 2"
            /><path d="M7.2 2.2 8 5.1" /><path
              d="M9.037 9.69a.498.498 0 0 1 .653-.653l11 4.5a.5.5 0 0 1-.074.949l-4.349 1.041a1 1 0 0 0-.74.739l-1.04 4.35a.5.5 0 0 1-.95.074z"
            /></svg
          >
          <p>
            {selectedRowIndexes.length === rows.length && rows.length > 0
              ? "ยกเลิกเลือกทั้งหมด"
              : "เลือกทั้งหมด"}
          </p>
        </button>
      </div>
      <span
        class="rounded-full bg-[#FFF5E8] px-3 py-1 font-semibold text-[#C65A00]"
        >กำลังดู: {getProblemFilterLabel(selectedProblemFilter)}</span
      >
      <span
        class="rounded-full bg-[#FFF5E8] px-3 py-1 font-semibold text-[#C65A00]"
        >แสดง {filteredRows.length} แถว</span
      >
      <span
        class="rounded-full bg-rose-50 px-3 py-1 font-semibold text-rose-700"
        >ในกลุ่มนี้มีปัญหา {visibleProblemRowCount()} แถว</span
      >
      <div class="flex-1 rounded-3xl items-center justify-center">
        <div class="flex justify-end space-x-4">
          <div
            class=" flex flex-wrap items-center gap-6 text-sm font-medium text-gray-800"
          >
            <div class="flex items-center gap-3">
              <span class="h-5 w-5 rounded-full bg-rose-400"></span>
              <span>แดง : {dangerRowCount()} แถว</span>
            </div>
            <div class="flex items-center gap-3">
              <span class="h-5 w-5 rounded-full bg-amber-400"></span>
              <span>ส้ม : 0 แถว</span>
            </div>
            <div class="flex items-center gap-3">
              <span class="h-5 w-5 rounded-full bg-yellow-300"></span>
              <span>เหลือง : {warningRowCount()} แถว</span>
            </div>
          </div>
          <button
            type="button"
            class="grid h-10 w-10 place-items-center rounded-full border border-gray-300 bg-white text-lg font-bold text-gray-700 shadow-sm transition-colors hover:bg-gray-50"
            on:click={() => (isLegendPopupOpen = !isLegendPopupOpen)}
            aria-label="ดูคำอธิบายสี"
          >
            ?
          </button>
        </div>
      </div>
    </div>

    <div
      class="min-h-0 flex-1 overflow-hidden rounded-3xl border border-[#FD6F02]/20 bg-white"
    >
      <div class="h-full overflow-auto">
        <table class="min-w-full border-collapse text-left">
          <thead class="sticky top-0 z-10 bg-[#FD8B1A] text-white">
            <tr>
              <th class="w-[58px] px-4 py-4 text-center">
                <input
                  type="checkbox"
                  checked={allVisibleRowsSelected}
                  on:change={toggleAllRows}
                  class="h-4 w-4 accent-[#FD6F02]"
                />
              </th>
              <th class="whitespace-nowrap px-4 py-4 font-semibold"
                >รหัสวิชา / ชื่อวิชา</th
              >

              <th class="whitespace-nowrap px-10 py-4 font-semibold"
                >คณะ/สาขา/หลักสูตร</th
              >
              <th class="whitespace-nowrap px-4 py-4 font-semibold">กลุ่ม</th>
              <th class="whitespace-nowrap px-24 py-4 font-semibold">ผู้สอน</th>
              <th class="whitespace-nowrap px-4 py-4 font-semibold"
                >ห้อง/อาคาร</th
              >
              <th class="whitespace-nowrap px-16 py-4 font-semibold"
                >วัน-เวลา</th
              >
              <th class="whitespace-nowrap px-24 py-4 font-semibold"
                >สอบกลางภาค</th
              >
              <th class="whitespace-nowrap px-24 py-4 font-semibold"
                >สอบปลายภาค</th
              >
              <th class="whitespace-nowrap px-12 py-4 font-semibold"
                >รายละเอียด</th
              >
              <th class="whitespace-nowrap px-4 py-4 font-semibold">จำนวนนศ.</th
              >
              <th class="whitespace-nowrap px-4 py-4 font-semibold">ปัญหา</th>
            </tr>
          </thead>
          <tbody>
            {#if filteredRows.length === 0}
              <tr>
                <td
                  colspan="13"
                  class="px-4 py-10 text-center text-sm text-gray-500"
                  >ไม่พบข้อมูลจาก readTable</td
                >
              </tr>
            {:else}
              {#each filteredRows as item}
                {@const row = item.row}
                {@const index = item.index}
                <tr
                  class="border-b border-gray-200 transition-colors"
                  class:bg-rose-100={getRowTone(row) === "danger"}
                  class:bg-amber-100={getRowTone(row) === "warning"}
                  class:bg-white={getRowTone(row) === "normal"}
                  class:bg-[#FFF5E8]={selectedRowIndexes.includes(index)}
                >
                  <td class="px-4 py-4 text-center">
                    <input
                      type="checkbox"
                      checked={selectedRowIndexes.includes(index)}
                      on:change={() => toggleRowSelection(index)}
                      class="h-4 w-4 accent-[#FD6F02]"
                    />
                  </td>
                  <td class="px-4 py-4 text-sm font-semibold text-gray-900">
                    <div>{row.course_id || "-"}</div>
                    <div class="text-xs font-normal text-gray-500">
                      {row.name_en || "-"}
                    </div>
                  </td>

                  <td class="px-4 py-4 text-sm text-gray-700">
                    <div class="font-medium text-gray-900">
                      {row.name_th || "-"}
                    </div>
                    <div class="text-xs text-gray-500">
                      {row.faculty || "-"}
                    </div>
                    <div class="text-xs text-gray-500">
                      {row.departments_name || "-"}
                    </div>
                    <div class="text-xs text-gray-500">
                      {row.program_name || "-"}
                    </div>
                    <div class="text-xs text-gray-500">
                      Program ID: {row.program_id ?? "-"}
                    </div>
                  </td>
                  <td class="px-4 py-4 text-sm text-gray-700"
                    >{row.group ?? "-"}</td
                  >
                  <td class="px-4 py-4 text-sm text-gray-700"
                    >{row.lecturer || "-"}</td
                  >
                  <td class="px-4 py-4 text-sm text-gray-700">
                    <div class="font-medium text-gray-900">
                      {row.room_name || row.room_id || "-"}
                    </div>
                    <div class="text-xs text-gray-500">
                      {row.building_name || "-"}
                    </div>
                    <div class="text-xs text-gray-500">
                      {row.building_number || "-"}
                    </div>
                  </td>
                  <td class="px-4 py-4 text-sm text-gray-700">
                    <div class="font-medium">{formatTimeRange(row)}</div>
                  </td>
                  <td class="px-4 py-4 text-sm text-gray-700">
                    <div class="font-medium">
                      {formatExamRange(
                        row.mid_day_name,
                        row.mid_day_number,
                        row.mid_start_time,
                        row.mid_end_time,
                      )}
                    </div>
                  </td>
                  <td class="px-4 py-4 text-sm text-gray-700">
                    <div class="font-medium">
                      {formatExamRange(
                        row.final_day_name,
                        row.final_day_number,
                        row.final_start_time,
                        row.final_end_time,
                      )}
                    </div>
                  </td>
                  <td class="px-4 py-4 text-sm text-gray-700">
                    <div>ประเภท: {row.type || "-"}</div>
                    <div class="text-xs text-gray-500">
                      Credit: {row.credit ?? "-"} | LPS: {row.lps || "-"}
                    </div>
                    <div class="text-xs text-gray-500">
                      ปีการศึกษา: {row.academic_year || "-"} | เทอม: {row.semester ??
                        "-"}
                    </div>
                  </td>
                  <td class="px-4 py-4 text-sm text-gray-700"
                    >{row.student_count ?? "-"}</td
                  >
                  <td class="px-4 py-4 text-sm text-gray-700"
                    >{formatProblems(row)}</td
                  >
                </tr>
              {/each}
            {/if}
          </tbody>
        </table>
      </div>
    </div>

    <div class="flex items-center justify-between gap-4">
      <button
        type="button"
        on:click={onBack}
        class="rounded-2xl bg-[#EAEAEA] px-6 py-3 text-[16px] font-semibold text-[#2D2D2D] shadow-sm transition-all hover:bg-[#d8d8d8] active:scale-98"
      >
        กลับไปเลือกไฟล์
      </button>

      <button
        type="button"
        on:click={onClose}
        class="rounded-2xl bg-[#FD6F02] px-6 py-3 text-[16px] font-semibold text-white shadow-sm transition-all hover:bg-[#e05f02] active:scale-98"
      >
        บันทึกและปิด
      </button>
    </div>
  </div>
</div>

<style>
  .overlay {
    position: fixed;
    inset: 0;
    padding: 16px;
    background: transparent;
  }
  .overlay > .backdrop {
    position: absolute;
    inset: 0;
  }
  .modal {
    position: fixed;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    z-index: 81;
    width: calc(100vw);
    max-width: 1480px;
    height: calc(100vh - 32px);
    max-height: 760px;
    display: flex;
    flex-direction: column;
    gap: 16px;
    overflow: hidden;
    border-radius: 32px;
    background: #fcfaf7;
    padding: 30px;
    box-shadow: 0 24px 64px rgba(0, 0, 0, 0.18);
  }
</style>
