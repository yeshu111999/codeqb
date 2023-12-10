<script lang="ts">
	import { SvelteUIProvider } from "@svelteuidev/core";

</script>

<svelte:head>
	<link rel="stylesheet" href={`../../themes/light.css`} />
</svelte:head>
<div>
	<SvelteUIProvider>
		<slot />
	</SvelteUIProvider>
</div>

<style>
	@import "../global.css";
</style>
