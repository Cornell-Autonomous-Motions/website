<script lang="ts">
	import camLogo from '$lib/images/CAM-logo.png';
	import autobike from '$lib/images/autobike.png';
	import lidar from '$lib/images/lidar.jpeg';
	import placeholder from '$lib/images/placeholder.png';

	let selectedIndex = -1;

	function selectImage(index: number) {
		selectedIndex = selectedIndex === index ? -1 : index;
	}

	function handleKeydown(event: KeyboardEvent, index: number) {
		if (event.key === 'Enter' || event.key === ' ') {
			event.preventDefault();
			selectImage(index);
		}
	}

	function handleGridLeave() {
		selectedIndex = -1;
	}

	function handleEscapeKey(event: KeyboardEvent) {
		if (event.key === 'Escape' && selectedIndex !== -1) {
			selectedIndex = -1;
		}
	}
</script>

<svelte:window on:keydown={handleEscapeKey} />

<section id="about" class="content-section">
	<div class="content-wrapper">
		<h2>About US</h2>
		<p>
	Cornell Autonomous Motions is a student organization at Cornell University dedicated to exploring and advancing autonomous systems and robotics. Evolving from Cornell's original autonomous bicycle team, our group has expanded its mission to develop a broader range of intelligent, self-navigating machines.
<br>
<br>
	Our current focus is the design and development of a fully autonomous quadruped robot—an agile four-legged platform capable of perceiving, learning from, and adapting to complex real-world environments. By integrating expertise across mechanical design, control systems, computer vision, and artificial intelligence, we aim to push the limits of robotic mobility and autonomy.
<br>
		</p>
	</div>
	
	<div 
		class="image-grid" 
		class:has-selection={selectedIndex !== -1}
		on:mouseleave={handleGridLeave}
	>
		<div 
			class="image-card tilt-left" 
			class:selected={selectedIndex === 0}
			on:click={() => selectImage(0)}
			on:keydown={(e) => handleKeydown(e, 0)}
			role="button"
			tabindex="0"
		>
			<img src={camLogo} alt="CAM Logo" />
			<div class="caption">Our Team Logo</div>
		</div>
		<div 
			class="image-card tilt-right" 
			class:selected={selectedIndex === 1}
			on:click={() => selectImage(1)}
			on:keydown={(e) => handleKeydown(e, 1)}
			role="button"
			tabindex="0"
		>
			<img src={autobike} alt="Autobike" />
			<div class="caption">Autonomous Bike Project</div>
		</div>
		<div 
			class="image-card tilt-slight" 
			class:selected={selectedIndex === 2}
			on:click={() => selectImage(2)}
			on:keydown={(e) => handleKeydown(e, 2)}
			role="button"
			tabindex="0"
		>
			<img src={lidar} alt="Lidar sensor" />
			<div class="caption">LiDAR Sensor Technology</div>
		</div>
		<div 
			class="image-card tilt-slight" 
			class:selected={selectedIndex === 3}
			on:click={() => selectImage(3)}
			on:keydown={(e) => handleKeydown(e, 3)}
			role="button"
			tabindex="0"
		>
			<img src={placeholder} alt="Placeholder" />
			<div class="caption">CAD Model</div>
		</div>
	</div>
</section>

<style>
	.content-section {
		position: relative;
		background-color: var(--color-bg-game);
		color: #A2998B;
		padding: 4rem 2rem;
		z-index: 2;
		min-height: 100vh;
	}

	.content-wrapper {
		max-width: 800px;
		margin: 0 auto;
	}

	.content-section h2 {
		color: #e13737;
		font-size: 5rem;
		margin-top: 2rem;
		margin-bottom: 1rem;
		font-family: 'Bitcount Grid Single', monospace;
	}

	.content-section h2:first-child {
		margin-top: 0;
	}

	.content-section p {
		font-size: 1.125rem;
		line-height: 1.6;
		margin-bottom: 1.5rem;
		font-family: monospace;
	}

	/* Image Grid */
	.image-grid {
		display: flex;
		justify-content: center;
		align-items: center;
		margin: 3rem auto;
		padding: 2rem 4rem;
		max-width: 1600px;
	}

	.image-card {
		position: relative;
		background: var(--color-bg-game);
		border: 8px solid var(--color-bg-game);
		border-radius: 12px;
		transition: all 0.4s ease;
		cursor: pointer;
		flex-shrink: 0;
		width: 300px;
	}

	/* All cards except first get negative margin for overlap */
	.image-card:not(:first-child) {
		margin-left: -15px;
		transition: all 0.4s ease;
	}

	/* Spread apart when hovering anywhere over the grid */
	.image-grid:hover .image-card:not(:first-child) {
		margin-left: 40px;
	}

	/* When a card is selected */
	.image-grid.has-selection .image-card:not(.selected) {
		opacity: 0.3;
		transform: scale(0.85);
		filter: blur(2px);
	}

	.image-grid.has-selection .image-card.selected {
		transform: translateY(-20px) scale(1.2) !important;
		z-index: 999;
		opacity: 1;
		filter: none;
	}

	.image-grid.has-selection .image-card.selected .caption {
		opacity: 1;
		bottom: -3rem;
		font-size: 1.25rem;
	}

	.image-card:hover {
		transform: translateY(-16px) scale(1.08);
	}

	.image-card img {
		width: 100%;
		height: auto;
		display: block;
		border-radius: 6px;
		transition: transform 0.4s ease;
	}

	.image-card:hover img {
		transform: scale(1.05);
	}

	.caption {
		position: absolute;
		bottom: -2.5rem;
		left: 50%;
		transform: translateX(-50%);
		font-size: 1.125rem;
		line-height: 1.6;
		color: #A2998B;
		opacity: 0;
		transition: opacity 0.3s ease;
		white-space: nowrap;
		pointer-events: none;
	}

	.image-card:hover .caption {
		opacity: 1;
	}

	.tilt-left {
		transform: rotate(-3deg);
	}

	.tilt-left:hover {
		transform: rotate(0deg) translateY(-16px) scale(1.08);
	}

	.tilt-right {
		transform: rotate(2.5deg);
	}

	.tilt-right:hover {
		transform: rotate(0deg) translateY(-16px) scale(1.08);
	}

	.tilt-slight {
		transform: rotate(-1.5deg);
	}

	.tilt-slight:hover {
		transform: rotate(0deg) translateY(-16px) scale(1.08);
	}

	/* Responsive design */
	@media (max-width: 768px) {
		.image-grid {
			flex-direction: column;
			gap: 2rem;
			padding: 2rem 1rem;
		}

		.image-card {
			width: 100%;
			max-width: 400px;
		}

		.image-card:nth-child(2),
		.image-card:nth-child(3) {
			margin-left: 0;
		}

		.tilt-left,
		.tilt-right,
		.tilt-slight {
			transform: rotate(0deg);
		}

		.image-card:hover {
			transform: translateY(-12px) scale(1.05);
		}

		.caption {
			font-size: 1.25rem;
		}
	}
</style>
