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
		classYear: number;
		department: string;
	}

	// Mock data
	const mockCourses: Course[] = [
		{
			id: 1,
			code: '05506003',
			nameEn: 'PROGRAMMING FUNDAMENTALS',
			nameTh: '...',
			credit: '...',
			lps: '...',
			classYear: 1,
			department: 'วิทยาการคอมพิวเตอร์'
		},
		{
			id: 2,
			code: '05506003',
			nameEn: 'PROGRAMMING FUNDAMENTALS',
			nameTh: '...',
			credit: '...',
			lps: '...',
			classYear: 1,
			department: 'วิทยาการคอมพิวเตอร์'
		},
		{
			id: 3,
			code: '05506003',
			nameEn: 'PROGRAMMING FUNDAMENTALS',
			nameTh: '...',
			credit: '...',
			lps: '...',
			classYear: 1,
			department: 'วิทยาการคอมพิวเตอร์'
		},
		{
			id: 4,
			code: '05506003',
			nameEn: 'PROGRAMMING FUNDAMENTALS',
			nameTh: '...',
			credit: '...',
			lps: '...',
			classYear: 1,
			department: 'วิทยาการคอมพิวเตอร์'
		},
		{
			id: 5,
			code: '05506005',
			nameEn: 'COMPUTER SCIENCE',
			nameTh: '...',
			credit: '...',
			lps: '...',
			classYear: 1,
			department: 'วิทยาการคอมพิวเตอร์'
		},
		{
			id: 6,
			code: '05506005',
			nameEn: 'COMPUTER SCIENCE',
			nameTh: '...',
			credit: '...',
			lps: '...',
			classYear: 1,
			department: 'วิทยาการคอมพิวเตอร์'
		},
		{
			id: 7,
			code: '05506005',
			nameEn: 'COMPUTER SCIENCE',
			nameTh: '...',
			credit: '...',
			lps: '...',
			classYear: 1,
			department: 'วิทยาการคอมพิวเตอร์'
		},
		{
			id: 8,
			code: '05506005',
			nameEn: 'COMPUTER SCIENCE',
			nameTh: '...',
			credit: '...',
			lps: '...',
			classYear: 1,
			department: 'วิทยาการคอมพิวเตอร์'
		}
	];

	// Filter states
	let searchQuery = '';
	let selectedClassYear = '1';
	let selectedDepartment = 'วิทยาการคอมพิวเตอร์';

	// Get unique values for filters
	$: classYears = [...new Set(mockCourses.map(c => c.classYear))].sort();
	$: departments = [...new Set(mockCourses.map(c => c.department))];

	// Filter courses
	$: filteredCourses = mockCourses.filter(course => {
		const matchesSearch =
			course.code.toLowerCase().includes(searchQuery.toLowerCase()) ||
			course.nameEn.toLowerCase().includes(searchQuery.toLowerCase());

		const matchesClassYear = !selectedClassYear || course.classYear === parseInt(selectedClassYear);
		const matchesDepartment = !selectedDepartment || course.department === selectedDepartment;

		return matchesSearch && matchesClassYear && matchesDepartment;
	});

	// Table columns
	const columns = [
		{ key: 'code', label: 'รหัสวิชา' },
		{ key: 'nameEn', label: 'ชื่อรายวิชา (EN)' },
		{ key: 'nameTh', label: 'ชื่อรายวิชา (TH)' },
		{ key: 'credit', label: 'หมวยเชิด' },
		{ key: 'lps', label: 'L-P-S' },
		{ key: 'manage', label: 'แก้ไข/ไบ' }
	];

	function handleEdit(row: Course) {
		console.log('Edit:', row);
	}

	function handleDelete(row: Course) {
		console.log('Delete:', row);
	}
</script>

<div class=" p-2">
	<div class="flex items-center justify-between mb-5">
		<h1 class="text-2xl font-semibold text-gray-800">จัดการรายวิชา</h1>
		<button class="px-6 py-2.5 bg-orange-500 text-white rounded-full font-medium hover:bg-orange-600 transition flex items-center gap-2">
			<span class="text-xl">+</span>
			<span>เพิ่มอันใหม่</span>
		</button>
	</div>

	<!-- Search -->
	<div class="mb-4">
		<SearchInput bind:search={searchQuery} placeholder="ค้นหารายวิชา" />
	</div>

	<!-- Filters -->
	<div class="flex gap-3 flex-wrap items-center mb-6">
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

	<!-- Table -->
	<Table {columns} rows={filteredCourses} onLoadMore={() => {}} HaveLoad={false}>
		<svelte:fragment slot="manage" let:row>
			<div class="flex gap-2">
				<button
					on:click={() => handleEdit(row)}
					class="p-2 bg-orange-500 text-white rounded-lg hover:bg-orange-600 transition flex items-center justify-center"
				>
					<svg xmlns="http://www.w3.org/2000/svg" width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
						<path d="M17 3a2.828 2.828 0 1 1 4 4L7.5 20.5 2 22l1.5-5.5L17 3z"></path>
					</svg>
				</button>
				<button
					on:click={() => handleDelete(row)}
					class="p-2 bg-red-500 text-white rounded-lg hover:bg-red-600 transition flex items-center justify-center"
				>
					<svg xmlns="http://www.w3.org/2000/svg" width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
						<polyline points="3 6 5 6 21 6"></polyline>
						<path d="M19 6v14a2 2 0 0 1-2 2H7a2 2 0 0 1-2-2V6m3 0V4a2 2 0 0 1 2-2h4a2 2 0 0 1 2 2v2"></path>
					</svg>
				</button>
			</div>
		</svelte:fragment>
	</Table>
</div>
