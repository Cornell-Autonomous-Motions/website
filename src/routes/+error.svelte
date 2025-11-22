<script>
    import GameOfLife from "$lib/GameOfLife.svelte";

</script>

<svelte:head>
	<title>Cornell Autonomous Motions</title>
	<meta name="description" content="Cornell Autonomous Motion Team" />
	<link rel="preconnect" href="https://fonts.googleapis.com">
	<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin="anonymous">
	<link href="https://fonts.googleapis.com/css2?family=Bitcount+Grid+Single&family=Caveat:wght@400;700&family=Covered+By+Your+Grace&display=swap" rel="stylesheet">
</svelte:head>

<br><br><br>

<section class = 'content-section'>
    <h2>404: Page not found.</h2>

    <p><a href="/">Return to home</a></p>
</section>

<style>
    .content-section {
		position: relative;
		background-color: var(--color-bg-game);
		color: #A2998B;
		padding: 4rem 2rem;
		z-index: 2;
	}

    .content-section p {
		font-size: 1.125rem;
		line-height: 1.6;
		margin-bottom: 1.5rem;
		font-family: monospace;
	}

    .content-section h2 {
		color: #e13737;
		font-size: 5rem;
		margin-top: 2rem;
		margin-bottom: 1rem;
		font-family: 'Bitcount Grid Single', monospace;
	}
</style>