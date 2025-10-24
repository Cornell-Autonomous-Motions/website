<script lang="ts">
	import GameOfLife from '$lib/GameOfLife.svelte';
	import AboutSection from './AboutSection.svelte';
	import Footer from './Footer.svelte';
</script>

<svelte:head>
	<title>Cornell Autonomous Motions</title>
	<meta name="description" content="Cornell Autonomous Motion Team" />
	<link rel="preconnect" href="https://fonts.googleapis.com">
	<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin="anonymous">
	<link href="https://fonts.googleapis.com/css2?family=Bitcount+Grid+Single&family=Caveat:wght@400;700&family=Covered+By+Your+Grace&display=swap" rel="stylesheet">
</svelte:head>

<div class="hero-game-container">
	<GameOfLife />
	<div class="hero-fade-overlay"></div>
</div>
<div class="top-fade"></div>
<div class="vignette"></div>

<AboutSection />
<Footer />

<style>
	.top-fade {
		position: fixed;
		top: 0;
		left: 0;
		width: 100vw;
		height: 30vh;
		pointer-events: none;
		background: linear-gradient(
			to bottom,
			var(--color-bg-game) 0%,
			transparent 100%
		);
		z-index: 1;
	}

	.vignette {
		position: fixed;
		top: 0;
		left: 0;
		width: 100vw;
		height: 100vh;
		pointer-events: none;
		background: radial-gradient(
			ellipse at center, 
			transparent 0%, 
			transparent 40%, 
			rgb(from var(--color-bg-game) r g b / 0.5) 80%, 
			rgb(from var(--color-bg-game) r g b / 0.8) 100%
		);
		z-index: 1;
	}

	.hero-game-container {
		position: relative;
		width: 100%;
		height: 120vh;
	}

	.hero-fade-overlay {
		position: absolute;
		bottom: 0;
		left: 0;
		width: 100%;
		height: 50vh;
		background: linear-gradient(to bottom, 
			transparent 0%, 
			rgb(from var(--color-bg-game) r g b / 0.3) 40%,
			rgb(from var(--color-bg-game) r g b / 0.7) 70%,
			var(--color-bg-game) 100%);
		pointer-events: none;
		z-index: 1;
	}
</style>
