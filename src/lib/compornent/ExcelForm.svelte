<script lang="ts">
  import { PUBLIC_API_URL } from "$env/static/public";
  import { openCourseExcelForm } from "$lib/stores/courseExcelForm";

  let selectedFiles: File[] = [];
  let isDragging = false;
  let fileInput: HTMLInputElement;
  let errorMessage = "";

  const close = () => {
    selectedFiles = [];
    errorMessage = "";
    openCourseExcelForm.set(false);
  };

  const triggerFileInput = () => {
    fileInput.click();
  };

  const processFiles = (fileList: FileList) => {
    let validFiles: File[] = [];
    let hasInvalid = false;

    for (let i = 0; i < fileList.length; i++) {
      const file = fileList[i];
      const extension = file.name.split('.').pop()?.toLowerCase();
      if (extension === 'xlsx' || extension === 'csv') {
        // หลีกเลี่ยงการเพิ่มไฟล์ซ้ำ
        if (!selectedFiles.some(f => f.name === file.name && f.size === file.size)) {
          validFiles.push(file);
        }
      } else {
        hasInvalid = true;
      }
    }

    if (validFiles.length > 0) {
      selectedFiles = [...selectedFiles, ...validFiles];
      errorMessage = "";
    }

    if (hasInvalid) {
      errorMessage = "กรุณาอัปโหลดไฟล์ที่มีนามสกุล .xlsx หรือ .csv เท่านั้น (บางไฟล์ถูกข้าม)";
    }
  };

  const handleFileChange = (e: Event) => {
    const target = e.target as HTMLInputElement;
    if (target.files && target.files.length > 0) {
      processFiles(target.files);
      target.value = ""; // เคลียร์ค่าของอินพุตเพื่อให้เลือกไฟล์เดิมซ้ำได้หากจำเป็น
    }
  };

  const handleDragOver = (e: DragEvent) => {
    e.preventDefault();
  };

  const handleDragEnter = (e: DragEvent) => {
    e.preventDefault();
    isDragging = true;
  };

  const handleDragLeave = (e: DragEvent) => {
    e.preventDefault();
    isDragging = false;
  };

  const handleDrop = (e: DragEvent) => {
    e.preventDefault();
    isDragging = false;
    if (e.dataTransfer && e.dataTransfer.files && e.dataTransfer.files.length > 0) {
      processFiles(e.dataTransfer.files);
    }
  };

  const removeFile = (index: number) => {
    selectedFiles = selectedFiles.filter((_, i) => i !== index);
  };

  const handleConfirm = async () => {
    if (selectedFiles.length === 0) return;

    try {
        const formData = new FormData()
        selectedFiles.forEach((file) => {
            formData.append("files", file);
        });

        const res = await fetch(`${PUBLIC_API_URL}/api/schedules/readTable`, {
        method: 'POST',
        body: formData
        })

        if (!res.ok){
            throw new Error("upload failed");
        }

        const data = await res.json();

        console.log(data);
        alert("สำเร็จ")
        close();
    }
    catch (err:any){
        alert("เกิดข้อผิดพลาด" + err.message);
    }
  };

  const handleCancel = () => {
    close();
  };

  const formatBytes = (bytes: number, decimals = 2) => {
    if (!+bytes) return '0 Bytes';
    const k = 1024;
    const dm = decimals < 0 ? 0 : decimals;
    const sizes = ['Bytes', 'KB', 'MB', 'GB'];
    const i = Math.floor(Math.log(bytes) / Math.log(k));
    return `${parseFloat((bytes / Math.pow(k, i)).toFixed(dm))} ${sizes[i]}`;
  };
</script>

<!-- Outer Container with exact dimensions 507 * 507 px -->
<div class="w-[507px] h-[507px] bg-[#FCFAF7] rounded-[32px] p-[38px] shadow-xl flex flex-col justify-between select-none box-border text-left my-font">
  
  <!-- Header -->
  <div>
    <h2 class="text-[32px] font-bold text-[#FD6F02] leading-tight">Upload files</h2>
  </div>

  <!-- Drag and Drop Area -->
  <div 
    class="flex-1 mt-5 border-2 border-dashed border-[#FD6F02] rounded-xl flex flex-col items-center justify-center bg-transparent transition-all duration-200"
    class:bg-[#FFF4EB]={isDragging}
    on:dragover={handleDragOver}
    on:dragenter={handleDragEnter}
    on:dragleave={handleDragLeave}
    on:drop={handleDrop}
  >
    <!-- Hidden input for file selection (supporting multiple files) -->
    <input 
      type="file" 
      accept=".xlsx, .csv" 
      multiple
      class="hidden" 
      bind:this={fileInput}
      on:change={handleFileChange}
    />

    {#if selectedFiles.length === 0}
      <!-- Upload Icon (Chevron Arrow + Bracket Tray) -->
      <svg 
        xmlns="http://www.w3.org/2000/svg" 
        viewBox="0 0 24 24" 
        fill="none" 
        stroke="#FD6F02" 
        stroke-width="2.5" 
        stroke-linecap="round" 
        stroke-linejoin="round" 
        class="w-16 h-16 text-[#FD6F02] mb-3"
      >
        <path d="M4 15v2a2 2 0 0 0 2 2h12a2 2 0 0 0 2-2v-2" />
        <line x1="12" y1="3" x2="12" y2="15" />
        <polyline points="7 8 12 3 17 8" />
      </svg>

      <!-- Drag & drop text -->
      <span class="text-[#FD6F02] text-[16px] font-medium">Drag files here to upload</span>
      
      <!-- Or text -->
      <span class="text-[#2D2D2D] text-[14px] my-1 font-normal">or</span>

      <!-- Browse file button -->
      <button 
        type="button"
        on:click={triggerFileInput}
        class="bg-[#FD6F02] text-white text-[14px] px-8 py-2 rounded-xl hover:bg-[#e05f02] font-semibold active:scale-95 transition-all cursor-pointer outline-none"
      >
        browse files
      </button>
    {:else}
      <!-- Multiple Files Selected State -->
      <div class="w-full flex flex-col items-center justify-between h-full py-4 px-2 box-border">
        
        <!-- Files Scrollable Container -->
        <div class="w-full flex flex-col gap-2 max-h-[155px] overflow-y-auto px-2">
          {#each selectedFiles as file, index}
            <div class="flex items-center justify-between bg-white/70 p-2.5 rounded-xl border border-[#FD6F02]/20 shadow-sm">
              <div class="flex items-center gap-2.5 overflow-hidden">
                <!-- File Icon -->
                <svg 
                  xmlns="http://www.w3.org/2000/svg" 
                  viewBox="0 0 24 24" 
                  fill="none" 
                  stroke="#FD6F02" 
                  stroke-width="2" 
                  stroke-linecap="round" 
                  stroke-linejoin="round" 
                  class="w-5 h-5 text-[#FD6F02] flex-shrink-0"
                >
                  <path d="M14.5 2H6a2 2 0 0 0-2 2v16a2 2 0 0 0 2 2h12a2 2 0 0 0 2-2V7.5L14.5 2z" />
                  <polyline points="14 2 14 8 20 8" />
                </svg>
                <span class="text-gray-800 text-[13px] font-semibold truncate max-w-[180px]" title={file.name}>
                  {file.name}
                </span>
              </div>
              <div class="flex items-center gap-2 flex-shrink-0">
                <span class="text-gray-500 text-[11px] font-medium">{formatBytes(file.size)}</span>
                
                <!-- Remove File Button -->
                <button 
                  type="button" 
                  on:click={() => removeFile(index)} 
                  class="text-red-400 hover:text-red-600 p-1 rounded-full hover:bg-red-50 cursor-pointer transition-colors"
                >
                  <svg xmlns="http://www.w3.org/2000/svg" width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round">
                    <line x1="18" y1="6" x2="6" y2="18"></line>
                    <line x1="6" y1="6" x2="18" y2="18"></line>
                  </svg>
                </button>
              </div>
            </div>
          {/each}
        </div>

        <!-- Add More Files Link -->
        <button 
          type="button"
          on:click={triggerFileInput}
          class="mt-2 text-[#FD6F02] hover:text-[#e05f02] text-[13px] font-bold cursor-pointer flex items-center gap-1 hover:underline transition-all"
        >
          <svg xmlns="http://www.w3.org/2000/svg" width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round" class="stroke-[#FD6F02]">
            <line x1="12" y1="5" x2="12" y2="19"></line>
            <line x1="5" y1="12" x2="19" y2="12"></line>
          </svg>
          เพิ่มไฟล์เพิ่มเติม
        </button>

      </div>
    {/if}
  </div>

  <!-- Allowed File Hint / Error Message -->
  <div class="mt-2.5 mb-4 text-left min-h-[18px]">
    {#if errorMessage}
      <span class="text-red-500 text-[12px] font-medium">{errorMessage}</span>
    {:else}
      <span class="text-[#FD6F02] text-[12px] font-medium">ไฟล์ .xlsx / .csv</span>
    {/if}
  </div>

  <!-- Footer Action Buttons -->
  <div class="grid grid-cols-2 gap-5 w-full">
    <button 
      type="button"
      disabled={selectedFiles.length === 0}
      on:click={handleConfirm}
      class="text-white font-semibold py-3 rounded-2xl text-[16px] outline-none shadow-sm transition-all"
      class:bg-[#EE4F00]={selectedFiles.length === 0}
      class:cursor-not-allowed={selectedFiles.length === 0}
      class:bg-[#FD6F02]={selectedFiles.length > 0}
      class:hover:bg-[#e05f02]={selectedFiles.length > 0}
      class:cursor-pointer={selectedFiles.length > 0}
      class:active:scale-98={selectedFiles.length > 0}
    >
      ยืนยัน
    </button>
    <button 
      type="button"
      on:click={handleCancel}
      class="bg-[#EAEAEA] text-[#2D2D2D] font-semibold py-3 rounded-2xl text-[16px] hover:bg-[#d8d8d8] active:scale-98 transition-all cursor-pointer outline-none shadow-sm"
    >
      ยกเลิก
    </button>
  </div>

</div>
