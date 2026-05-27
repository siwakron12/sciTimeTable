<script lang="ts">
    export let data: any = null; // ถ้ามีคือแก้ไข ถ้าไม่มีคือสร้างใหม่

    // ใช้ค่าเริ่มต้น ถ้า data ไม่มี ให้เป็นค่าว่าง
    type Form = {
        thaiName: string;
        engName: string;
        code: string;
        curriculum: string;
        category: string;
        type: string;
        credit: string;
        pso: string;
        department: string;
        year: string;
        time: string;
        teacher: string;
        room: string;
        building: string;
        midExam: string;
        finalExam: string;
        description: string;
        note: string;
    };

    let form: Form = {
        thaiName: data?.thaiName ?? "",
        engName: data?.engName ?? "",
        code: data?.code ?? "",
        curriculum: data?.curriculum ?? "",
        category: data?.category ?? "",
        type: data?.type ?? "",
        credit: data?.credit ?? "",
        pso: data?.pso ?? "",
        department: data?.department ?? "",
        year: data?.year ?? "",
        time: data?.time ?? "",
        teacher: data?.teacher ?? "",
        room: data?.room ?? "",
        building: data?.building ?? "",
        midExam: data?.midExam ?? "",
        finalExam: data?.finalExam ?? "",
        description: data?.description ?? "",
        note: data?.note ?? "",
    };
</script>

<!-- ข้อมูลรายวิชา -->
<section class="bg-orange-50 border border-orange-200 rounded-xl p-6 mb-6">
    <h2 class="text-xl font-semibold mb-4">ข้อมูลรายวิชา</h2>
    <div class="grid grid-cols-2 gap-4">
        <div>
            <label>ชื่อย่อ / ไทย *</label>
            <input
                class="input border border-gray-300 rounded w-full px-2 py-1"
                placeholder="เช่น วิชาวิทยาศาสตร์ทั่วไป"
            />
        </div>
        <div>
            <label>ชื่อวิชา (อังกฤษ) *</label>
            <input
                class="input border border-gray-300 rounded w-full px-2 py-1"
                placeholder="เช่น General Science"
            />
        </div>
        <div>
            <label>รหัสวิชา *</label>
            <input
                class="input border border-gray-300 rounded w-full px-2 py-1"
                placeholder="เช่น SCI101"
            />
        </div>
        <div>
            <label>หลักสูตร *</label>
            <input
                class="input border border-gray-300 rounded w-full px-2 py-1"
                placeholder="เช่น ปริญญาตรีวิทยาศาสตร์"
            />
        </div>
        <div>
            <label>หมวดวิชา *</label>
            <input
                class="input border border-gray-300 rounded w-full px-2 py-1"
                placeholder="เช่น วิทยาศาสตร์พื้นฐาน"
            />
        </div>
        <div>
            <label>ประเภท *</label>
            <input
                class="input border border-gray-300 rounded w-full px-2 py-1"
                placeholder="เช่น บังคับ/เลือก"
            />
        </div>
        <div>
            <label>หน่วยกิต *</label>
            <input
                class="input border border-gray-300 rounded w-full px-2 py-1"
                placeholder="เช่น 3(2-2-5)"
            />
        </div>
        <div>
            <label>ป-ส-อ *</label>
            <input
                class="input border border-gray-300 rounded w-full px-2 py-1"
                placeholder="เช่น 2-2-0"
            />
        </div>
    </div>
</section>

<!-- ข้อมูลการเรียนการสอน -->
<section class="bg-orange-50 border border-orange-200 rounded-xl p-6 mb-6">
    <h2 class="text-xl font-semibold mb-4">ข้อมูลการเรียนการสอน</h2>
    <div class="grid grid-cols-2 gap-4">
        <div>
            <label>สาขา/ภาควิชา *</label>
            <input
                class="input border border-gray-300 rounded w-full px-2 py-1"
                placeholder="เช่น วิทยาศาสตร์ทั่วไป"
            />
        </div>
        <div>
            <label>ปีการศึกษา *</label>
            <input
                class="input border border-gray-300 rounded w-full px-2 py-1"
                placeholder="เช่น 2568"
            />
        </div>
        <div>
            <label>วัน–เวลาที่เรียน *</label>
            <input
                class="input border border-gray-300 rounded w-full px-2 py-1"
                placeholder="เช่น ทุกวันพุธ 08:30–10:30"
            />
        </div>
        <div>
            <label>อาจารย์ผู้สอน *</label>
            <input
                class="input border border-gray-300 rounded w-full px-2 py-1"
                placeholder="เช่น ผศ.ดร.สมชาย"
            />
        </div>
        <div>
            <label>ห้องเรียน</label>
            <input
                class="input border border-gray-300 rounded w-full px-2 py-1"
                placeholder="เช่น ห้อง 101"
            />
        </div>
        <div>
            <label>ตึก</label>
            <input
                class="input border border-gray-300 rounded w-full px-2 py-1"
                placeholder="เช่น ตึกวิทยาศาสตร์"
            />
        </div>
        <div>
            <label>วัน–เวลาออกสอบกลางภาค</label>
            <input
                class="input border border-gray-300 rounded w-full px-2 py-1"
                placeholder="เช่น 15 มีนาคม 2568 09:00"
            />
        </div>
        <div>
            <label>วัน–เวลาออกสอบปลายภาค</label>
            <input
                class="input border border-gray-300 rounded w-full px-2 py-1"
                placeholder="เช่น 10 มิถุนายน 2568 13:00"
            />
        </div>
    </div>
</section>

<!-- อื่น ๆ -->
<section class="bg-orange-50 border border-orange-200 rounded-xl p-6 mb-6">
    <h2 class="text-xl font-semibold mb-4">อื่น ๆ</h2>
    <div class="grid grid-cols-2 gap-4">
        <div>
            <label>คำอธิบาย</label>
            <input
                class="input border border-gray-300 rounded w-full px-2 py-1"
                placeholder="รายละเอียดเพิ่มเติม"
            />
        </div>
        <div>
            <label>หมายเหตุ</label>
            <input
                class="input border border-gray-300 rounded w-full px-2 py-1"
                placeholder="หมายเหตุอื่น ๆ"
            />
        </div>
    </div>
</section>

<!-- ปุ่มบันทึก -->
<div class="flex justify-end">
    <button
        class="bg-orange-500 text-white px-6 py-3 rounded-lg font-semibold shadow-md hover:bg-orange-600"
    >
        บันทึก
    </button>
</div>
