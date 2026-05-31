<script lang="ts">
	import Table from '$lib/compornent/Table.svelte';
	import SearchInput from '$lib/compornent/SearchInput.svelte';

	interface Course {
		id: number;
		code: string;
		nameEn: string;
		nameTh: string;
		credit: string;
		lps: string;
		academicYear: number;
		semester: number;
		classYear: number;
		department: string;
	}

	const mockCourses: Course[] = [
		{ id: 1, code: '05506003', nameEn: 'PROGRAMMING FUNDAMENTALS', nameTh: '...', credit: '3', lps: '2-2-5', academicYear: 2567, semester: 1, classYear: 1, department: 'วิทยาการคอมพิวเตอร์' },
		{ id: 2, code: '05506003', nameEn: 'PROGRAMMING FUNDAMENTALS', nameTh: '...', credit: '3', lps: '2-2-5', academicYear: 2567, semester: 1, classYear: 1, department: 'วิทยาการคอมพิวเตอร์' },
		{ id: 3, code: '05506003', nameEn: 'PROGRAMMING FUNDAMENTALS', nameTh: '...', credit: '3', lps: '2-2-5', academicYear: 2567, semester: 1, classYear: 1, department: 'วิทยาการคอมพิวเตอร์' },
		{ id: 4, code: '05506003', nameEn: 'PROGRAMMING FUNDAMENTALS', nameTh: '...', credit: '3', lps: '2-2-5', academicYear: 2567, semester: 1, classYear: 1, department: 'วิทยาการคอมพิวเตอร์' },
		{ id: 5, code: '05506005', nameEn: 'COMPUTER SCIENCE', nameTh: '...', credit: '3', lps: '2-2-5', academicYear: 2567, semester: 1, classYear: 1, department: 'วิทยาการคอมพิวเตอร์' },
		{ id: 6, code: '05506005', nameEn: 'COMPUTER SCIENCE', nameTh: '...', credit: '3', lps: '2-2-5', academicYear: 2567, semester: 1, classYear: 1, department: 'วิทยาการคอมพิวเตอร์' },
		{ id: 7, code: '05506005', nameEn: 'COMPUTER SCIENCE', nameTh: '...', credit: '3', lps: '2-2-5', academicYear: 2567, semester: 1, classYear: 1, department: 'วิทยาการคอมพิวเตอร์' },
		{ id: 8, code: '05506005', nameEn: 'COMPUTER SCIENCE', nameTh: '...', credit: '3', lps: '2-2-5', academicYear: 2567, semester: 1, classYear: 1, department: 'วิทยาการคอมพิวเตอร์' }
	];

	let searchQuery = '';
	let selectedAcademicYear = '2567';
	let selectedSemester = '1';
	let selectedClassYear = '1';
	let selectedDepartment = 'วิทยาการคอมพิวเตอร์';

	$: academicYears = [...new Set(mockCourses.map(course => course.academicYear))].sort((a, b) => b - a);
	$: semesters = [...new Set(mockCourses.map(course => course.semester))].sort();
	$: classYears = [...new Set(mockCourses.map(course => course.classYear))].sort();
	$: departments = [...new Set(mockCourses.map(course => course.department))];

	$: filteredCourses = mockCourses.filter(course => {
		const search = searchQuery.toLowerCase();
		const matchesSearch =
			course.code.toLowerCase().includes(search) ||
			course.nameEn.toLowerCase().includes(search) ||
			course.nameTh.toLowerCase().includes(search);

		const matchesAcademicYear = !selectedAcademicYear || course.academicYear === parseInt(selectedAcademicYear);
		const matchesSemester = !selectedSemester || course.semester === parseInt(selectedSemester);
		const matchesClassYear = !selectedClassYear || course.classYear === parseInt(selectedClassYear);
		const matchesDepartment = !selectedDepartment || course.department === selectedDepartment;

		return matchesSearch && matchesAcademicYear && matchesSemester && matchesClassYear && matchesDepartment;
	});

	const columns = [
		{ key: 'code', label: 'รหัสวิชา' },
		{ key: 'nameEn', label: 'ชื่อรายวิชา (EN)' },
		{ key: 'nameTh', label: 'ชื่อรายวิชา (TH)' },
		{ key: 'credit', label: 'หน่วยกิต' },
		{ key: 'lps', label: 'L-P-S' },
		{ key: 'manage', label: 'แก้ไข' }
	];

	function handleEdit(row: Course) {
		console.log('Edit course:', row);
	}

	function handleDelete(row: Course) {
		console.log('Delete course:', row);
	}
</script>

<div class=" p-2">
	<div class="flex items-center justify-between mb-5">
		<h1 class="text-2xl font-semibold text-gray-800">รายวิชาทั้งหมด</h1>
		<button class="px-6 py-2.5 bg-orange-500 text-white rounded-full font-medium hover:bg-orange-600 transition flex items-center gap-2">
			<span class="text-xl leading-none">+</span>
			<span>เพิ่มข้อมูล</span>
		</button>
	</div>

	<div class="mb-4">
		<SearchInput bind:search={searchQuery} placeholder="ค้นหารายวิชา" />
	</div>

	<div class="flex gap-3 flex-wrap items-center mb-6">
		<div class="flex items-center gap-2">
			<span class="text-sm font-medium text-gray-700">ปีการศึกษา</span>
			<select bind:value={selectedAcademicYear} class="px-4 py-2 border-2 border-gray-400 rounded-full bg-white cursor-pointer text-sm font-medium appearance-none pr-6 text-gray-700 hover:border-gray-500 transition">
				<option value="">2567</option>
				{#each academicYears as year}
					<option value={year}>{year}</option>
				{/each}
			</select>
		</div>

		<div class="flex items-center gap-2">
			<span class="text-sm font-medium text-gray-700">ภาคเรียนที่</span>
			<select bind:value={selectedSemester} class="px-4 py-2 border-2 border-gray-400 rounded-full bg-white cursor-pointer text-sm font-medium appearance-none pr-6 text-gray-700 hover:border-gray-500 transition">
				<option value="">1</option>
				{#each semesters as sem}
					<option value={sem}>{sem}</option>
				{/each}
			</select>
		</div>

		<div class="flex items-center gap-2">
			<span class="text-sm font-medium text-gray-700">ชั้นปีที่</span>
			<select bind:value={selectedClassYear} class="px-4 py-2 border-2 border-gray-400 rounded-full bg-white cursor-pointer text-sm font-medium appearance-none pr-6 text-gray-700 hover:border-gray-500 transition">
				<option value="">1</option>
				{#each classYears as year}
					<option value={year}>{year}</option>
				{/each}
			</select>
		</div>

		<div class="flex items-center gap-2">
			<span class="text-sm font-medium text-gray-700">ภาควิชา</span>
			<select bind:value={selectedDepartment} class="px-4 py-2 border-2 border-gray-400 rounded-full bg-white cursor-pointer text-sm font-medium appearance-none pr-6 text-gray-700 hover:border-gray-500 transition">
				<option value="">วิทยาการคอมพิวเตอร์</option>
				{#each departments as dept}
					<option value={dept}>{dept}</option>
				{/each}
			</select>
		</div>
	</div>

	<Table {columns} rows={filteredCourses} onLoadMore={() => {}} HaveLoad={false}>
		<svelte:fragment slot="manage" let:row>
			<div class="flex gap-2 justify-center">
				<button
					on:click={() => handleEdit(row)}
					class="w-10 h-10 bg-orange-500 text-white rounded-xl hover:bg-orange-600 transition flex items-center justify-center"
					aria-label="แก้ไข"
				>
					<svg xmlns="http://www.w3.org/2000/svg" width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
						<path d="M12 20h9" />
						<path d="M16.5 3.5a2.1 2.1 0 0 1 3 3L7 19l-4 1 1-4Z" />
					</svg>
				</button>
				<button
					on:click={() => handleDelete(row)}
					class="w-10 h-10 bg-red-500 text-white rounded-xl hover:bg-red-600 transition flex items-center justify-center"
					aria-label="ลบ"
				>
					<svg xmlns="http://www.w3.org/2000/svg" width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
						<path d="M3 6h18" />
						<path d="M8 6V4a1 1 0 0 1 1-1h6a1 1 0 0 1 1 1v2" />
						<path d="M19 6v14a2 2 0 0 1-2 2H7a2 2 0 0 1-2-2V6" />
						<path d="M10 11v6" />
						<path d="M14 11v6" />
					</svg>
				</button>
			</div>
		</svelte:fragment>
	</Table>
</div>