<script lang="ts">
	import GameOfLife from '$lib/GameOfLife.svelte';
</script>

<svelte:head>
	<title>Cornell Autonomous Motion</title>
	<meta name="description" content="Cornell Autonomous Motion Team" />
</svelte:head>

<GameOfLife />
<div class="vignette"></div>

<style>
	.vignette {
		position: fixed;
		top: 0;
		left: 0;
		width: 100vw;
		height: 100vh;
		pointer-events: none;
		background: radial-gradient(ellipse at center, transparent 0%, transparent 40%, #22222280 80%, #222222cc 100%);
		z-index: 1;
	}
</style>
