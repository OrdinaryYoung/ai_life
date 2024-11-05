<script lang="ts">
	import '../app.css';
	import { page } from '$app/stores';

	let { children } = $props();
</script>

<svelte:head>
	<title>{$page.data.title}</title>
</svelte:head>

{@render children()}
