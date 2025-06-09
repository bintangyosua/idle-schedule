<script lang="ts">
	import '../app.css';
	import '@fontsource-variable/caveat';

	let { children } = $props();

	export const prerender = true;
</script>

{@render children()}
