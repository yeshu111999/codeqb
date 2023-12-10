<script lang="ts">
	import { createEventDispatcher, onDestroy, onMount } from "svelte";
	import { cubicOut } from "svelte/easing";
	import { fade } from "svelte/transition";
	import Portal from "./Portal.svelte";
	import { browser } from "$app/environment";

	export let width = "max-w-sm";

	let backdropEl;
	let modalEl;

	const dispatch = createEventDispatcher();

	function handleKeydown(event) {
		// close on ESC
		if (event.key === "Escape") {
			event.preventDefault();
			dispatch("close");
		}
	}

	function handleBackdropClick(event) {
		if (event.target === backdropEl) {
			dispatch("close");
		}
	}

	onMount(() => {
		document.getElementById("app")?.setAttribute("inert", "true");
		modalEl.focus();
	});

	onDestroy(() => {
		if (!browser) return;
		// remove inert attribute if this is the last modal
		if (document.querySelectorAll('[role="dialog"]:not(#app *)').length === 1) {
			document.getElementById("app")?.removeAttribute("inert");
		}
	});
</script>

<Portal>
	<div
		role="presentation"
		tabindex="-1"
		bind:this={backdropEl}
		on:click={handleBackdropClick}
		transition:fade={{ easing: cubicOut, duration: 300 }}
		class="overlay"
	>
		<div
			role="dialog"
			tabindex="-1"
			bind:this={modalEl}
			on:keydown={handleKeydown}
			class="popup-container"
		>
			<slot />
		</div>
	</div>
</Portal>

<style>
	.scrollbar-hide::-webkit-scrollbar {
		width: 0 !important;
	}
	.scrollbar-hide {
		overflow: -moz-scrollbars-none;
	}
	.scrollbar-hide {
		-ms-overflow-style: none;
	}

	.overlay, .popup-container {
		width: 100vw;
		height: 100vh;
		background-color: #DDDDDD;
		display: flex;
		justify-content: center;
		align-items: center;
		overflow: hidden;
		position: absolute;
		top: 0;
		z-index: 99;
	}
</style>
