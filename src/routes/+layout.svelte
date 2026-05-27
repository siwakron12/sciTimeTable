<script lang="ts">
	import favicon from "$lib/assets/favicon.svg";
	import Nav from "$lib/MyLayout/Nav.svelte";
	import SideBar from "$lib/MyLayout/SideBar.svelte";
	import CourseFormDrawer from "$lib/compornent/CourseFormDrawer.svelte";
	import CourseFormExcelDrawer from "$lib/compornent/CourseExcelDrawer.svelte";
	import "./app.css";

	import { openCourseForm } from "$lib/stores/courseForm";
	// import { openCourseExcelForm} from "$lib/stores/courseExcelForm";
  import CourseExcelDrawer from "$lib/compornent/CourseExcelDrawer.svelte";

	let { children } = $props();
</script>

<svelte:head>
	<link rel="icon" href={favicon} />
</svelte:head>
<div class="h-screen flex flex-col">
	<Nav />

	<div class="flex flex-1">
		<SideBar  />
		<div class="flex-1 overflow-x-auto">
			<div class="min-w-max border-collapse w-full p-8">
				{@render children()}
			</div>
		</div>
	</div>

	<CourseFormDrawer />
	<CourseExcelDrawer />
</div>
