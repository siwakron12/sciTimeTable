<script lang="ts">
	import Table from '$lib/compornent/Table.svelte';
	import SearchInput from '$lib/compornent/SearchInput.svelte';

	interface Course {
		code: string;
		name: string;
		type: string;
		group: number;
		schedule: string;
		room: string;
		floor: string;
		instructor: string;
		academicYear: number;
		semester: number;
		classYear: number;
		department: string;
	}

	// Mock data
	const mockCourses: Course[] = [
		{
			code: '05506003',
			name: 'PROGRAMMING FUNDAMENTALS',
			type: 'ทฤษฎี',
			group: 1,
			schedule: 'จ.10:30-12:30',
			room: '313',
			floor: 'พระจอมเกล้า',
			instructor: 'อ.ศรีรัฒน์- อุดสุยัง',
			academicYear: 2567,
			semester: 1,
			classYear: 1,
			department: 'วิทยาการคอมพิวเตอร์'
		},
		{
			code: '05506003',
			name: 'PROGRAMMING FUNDAMENTALS',
			type: 'ทฤษฎี',
			group: 2,
			schedule: 'จ.08:30-10:30',
			room: '313',
			floor: 'พระจอมเกล้า',
			instructor: 'อ.ศรีรัฒน์- อุดสุยัง',
			academicYear: 2567,
			semester: 1,
			classYear: 1,
			department: 'วิทยาการคอมพิวเตอร์'
		},
		{
			code: '05506003',
			name: 'PROGRAMMING FUNDAMENTALS',
			type: 'ปฏิบัติ',
			group: 3,
			schedule: 'อ.15:00-17:00',
			room: '211',
			floor: 'จุฬาการณ์ 2',
			instructor: 'อ.ศรีรัฒน์- อุดสุยัง',
			academicYear: 2567,
			semester: 1,
			classYear: 1,
			department: 'วิทยาการคอมพิวเตอร์'
		},
		{
			code: '05506003',
			name: 'PROGRAMMING FUNDAMENTALS',
			type: 'ปฏิบัติ',
			group: 4,
			schedule: 'อ.13:00-15:00',
			room: '211',
			floor: 'จุฬาการณ์ 2',
			instructor: 'อ.ศรีรัฒน์- อุดสุยัง',
			academicYear: 2567,
			semester: 1,
			classYear: 1,
			department: 'วิทยาการคอมพิวเตอร์'
		},
		{
			code: '05506005',
			name: 'COMPUTER SCIENCE',
			type: 'ทฤษฎี',
			group: 1,
			schedule: 'พ.13:00-15:00',
			room: '224',
			floor: 'จุฬาการณ์ 1',
			instructor: 'อ.ศรีรัฒน์- อุดสุยัง',
			academicYear: 2567,
			semester: 1,
			classYear: 1,
			department: 'วิทยาการคอมพิวเตอร์'
		},
		{
			code: '05506005',
			name: 'COMPUTER SCIENCE',
			type: 'ทฤษฎี',
			group: 2,
			schedule: 'ก.13:00-15:00',
			room: '211',
			floor: 'จุฬาการณ์ 2',
			instructor: 'อ.ศรีรัฒน์- อุดสุยัง',
			academicYear: 2567,
			semester: 1,
			classYear: 1,
			department: 'วิทยาการคอมพิวเตอร์'
		},
		{
			code: '05506005',
			name: 'COMPUTER SCIENCE',
			type: 'ปฏิบัติ',
			group: 3,
			schedule: 'ศ.15:00-17:00',
			room: '224',
			floor: 'จุฬาการณ์ 1',
			instructor: 'อ.ศรีรัฒน์- อุดสุยัง',
			academicYear: 2567,
			semester: 1,
			classYear: 1,
			department: 'วิทยาการคอมพิวเตอร์'
		},
		{
			code: '05506005',
			name: 'COMPUTER SCIENCE',
			type: 'ปฏิบัติ',
			group: 4,
			schedule: 'ก.15:00-17:00',
			room: '211',
			floor: 'จุฬาการณ์ 2',
			instructor: 'อ.ศรีรัฒน์- อุดสุยัง',
			academicYear: 2567,
			semester: 1,
			classYear: 1,
			department: 'วิทยาการคอมพิวเตอร์'
		}
	];

	// Filter states
	let searchQuery = '';
	let selectedAcademicYear = '2567';
	let selectedSemester = '1';
	let selectedClassYear = '1';
	let selectedDepartment = 'วิทยาการคอมพิวเตอร์';

	// Get unique values for filters
	$: academicYears = [...new Set(mockCourses.map(c => c.academicYear))].sort((a, b) => b - a);
	$: semesters = [...new Set(mockCourses.map(c => c.semester))].sort();
	$: classYears = [...new Set(mockCourses.map(c => c.classYear))].sort();
	$: departments = [...new Set(mockCourses.map(c => c.department))];

	// Filter courses
	$: filteredCourses = mockCourses.filter(course => {
		const matchesSearch =
			course.code.toLowerCase().includes(searchQuery.toLowerCase()) ||
			course.name.toLowerCase().includes(searchQuery.toLowerCase());

		const matchesAcademicYear = !selectedAcademicYear || course.academicYear === parseInt(selectedAcademicYear);
		const matchesSemester = !selectedSemester || course.semester === parseInt(selectedSemester);
		const matchesClassYear = !selectedClassYear || course.classYear === parseInt(selectedClassYear);
		const matchesDepartment = !selectedDepartment || course.department === selectedDepartment;

		return matchesSearch && matchesAcademicYear && matchesSemester && matchesClassYear && matchesDepartment;
	});

	// Table columns
	const columns = [
		{ key: 'code', label: 'รหัสวิชา' },
		{ key: 'name', label: 'ชื่อรายวิชา' },
		{ key: 'type', label: 'ประเภท' },
		{ key: 'group', label: 'กลุ่มเรียน' },
		{ key: 'schedule', label: 'วัน-เวลาเรียน' },
		{ key: 'room', label: 'ห้องเรียน' },
		{ key: 'floor', label: 'ดัก' },
		{ key: 'instructor', label: 'อาจารผู้สอน' }
	];
</script>

<div class=" p-2">
	<h1 class="text-2xl font-semibold mb-5 text-gray-800">ตารางเรียนทั้งหมด</h1>

	<!-- Filters -->
	<div class="mb-6">
		<div class="mb-4">
			<SearchInput bind:search={searchQuery} placeholder="ค้นหารายวิชา" />
		</div>

		<div class="flex gap-3 flex-wrap items-center r">
			<div class="flex items-center gap-2 ">
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
	</div>

	<!-- Table -->
	<Table {columns} rows={filteredCourses} onLoadMore={() => {}} HaveLoad={false} />
</div>
