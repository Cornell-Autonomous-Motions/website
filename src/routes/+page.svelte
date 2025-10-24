<script lang="ts">
	import GameOfLife from '$lib/GameOfLife.svelte';
	import AboutSection from './AboutSection.svelte';
	import Footer from './Footer.svelte';
	
	let showModal = false;
	
	function toggleModal() {
		showModal = !showModal;
	}
	
	function closeModal() {
		showModal = false;
	}
</script>

<svelte:head>
	<title>Cornell Autonomous Motions</title>
	<meta name="description" content="Cornell Autonomous Motion Team" />
	<link rel="preconnect" href="https://fonts.googleapis.com">
	<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin="anonymous">
	<link href="https://fonts.googleapis.com/css2?family=Bitcount+Grid+Single&family=Caveat:wght@400;700&family=Covered+By+Your+Grace&display=swap" rel="stylesheet">
</svelte:head>

<GameOfLife />
<div class="top-fade"></div>
<div class="vignette"></div>

<!-- Question Mark Icon Button -->
<button class="info-button" on:click={toggleModal} aria-label="About the background">
	?
</button>

<!-- Information Modal -->
{#if showModal}
	<div class="modal-backdrop" on:click={closeModal} role="presentation">
		<div class="modal-content" on:click|stopPropagation role="dialog" aria-modal="true">
			<button class="close-button" on:click={closeModal} aria-label="Close">×</button>
			<h2>Conway's Game of Life</h2>
			<div class="modal-body">
				<p>
					The animated background you see is <strong>Conway's Game of Life</strong>, a cellular automaton 
					created by mathematician John Conway in 1970. It's not a traditional game, but rather a 
					zero-player simulation where cells evolve based on simple rules:
				</p>
				<ul>
					<li>A live cell with 2-3 neighbors survives</li>
					<li>A dead cell with exactly 3 neighbors becomes alive</li>
					<li>All other cells die or stay dead</li>
				</ul>
				<p>
					Despite these simple rules, the Game of Life can produce incredibly complex and emergent behaviors—
					patterns that move, grow, and interact in surprising ways.
				</p>
				<h3>Why CAM Chose This Background</h3>
				<p>
					The Game of Life perfectly embodies the principles of <strong>autonomous systems</strong>:
				</p>
				<ul>
					<li><strong>Emergence:</strong> Complex behaviors arise from simple local rules, just like how 
					autonomous vehicles make sophisticated decisions from basic sensor inputs and algorithms</li>
					<li><strong>Self-Organization:</strong> The system evolves without central control, mirroring 
					how autonomous systems adapt to their environment independently</li>
					<li><strong>Local Interactions:</strong> Each cell only considers its immediate neighbors, 
					similar to how autonomous agents make decisions based on local sensor data</li>
					<li><strong>Unpredictability:</strong> Small changes can lead to vastly different outcomes, 
					highlighting the importance of robust testing and validation in autonomous systems</li>
				</ul>
				<p>
					This visual metaphor reminds us that elegant, autonomous behavior doesn't require complexity—
					it requires the right rules and careful engineering.
				</p>
			</div>
		</div>
	</div>
{/if}

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

	/* Info Button */
	.info-button {
		position: fixed;
		bottom: 20px;
		right: 20px;
		width: 50px;
		height: 50px;
		border-radius: 25px;
		background-color: #A2998B;
		color: var(--color-bg-game);
		border: none;
		font-size: 28px;
		font-weight: bold;
		cursor: pointer;
		z-index: 9999;
		pointer-events: auto;
		transition: all 0.3s ease;
		box-shadow: 0 4px 12px rgba(0, 0, 0, 0.3);
		display: flex;
		align-items: center;
		justify-content: center;
		font-family: 'Bitcount Grid Single', monospace;
	}

	.info-button:hover {
		background-color: #e13737;
		transform: scale(1.1);
		box-shadow: 0 6px 16px rgba(225, 55, 55, 0.4);
	}

	.info-button:active {
		transform: scale(0.95);
	}

	/* Modal */
	.modal-backdrop {
		position: fixed;
		top: 0;
		left: 0;
		width: 100vw;
		height: 100vh;
		background-color: rgba(0, 0, 0, 0.7);
		z-index: 10000;
		pointer-events: auto;
		display: flex;
		align-items: center;
		justify-content: center;
		padding: 20px;
		backdrop-filter: blur(4px);
	}

	.modal-content {
		background-color: var(--color-bg-game);
		color: #A2998B;
		border-radius: 16px;
		max-width: 700px;
		width: 100%;
		max-height: 85vh;
		overflow-y: auto;
		padding: 2.5rem;
		position: relative;
		box-shadow: 0 10px 40px rgba(0, 0, 0, 0.5);
		border: 2px solid #A2998B;
		pointer-events: auto;
	}

	.close-button {
		position: absolute;
		top: 15px;
		right: 15px;
		background: none;
		border: none;
		color: #A2998B;
		font-size: 36px;
		cursor: pointer;
		width: 40px;
		height: 40px;
		display: flex;
		align-items: center;
		justify-content: center;
		border-radius: 8px;
		transition: all 0.2s ease;
		line-height: 1;
		pointer-events: auto;
	}

	.close-button:hover {
		background-color: rgba(162, 153, 139, 0.1);
		color: #e13737;
	}

	.modal-content h2 {
		color: #e13737;
		font-size: 2.5rem;
		margin-top: 0;
		margin-bottom: 1.5rem;
		font-family: 'Bitcount Grid Single', monospace;
	}

	.modal-content h3 {
		color: #e13737;
		font-size: 1.75rem;
		margin-top: 2rem;
		margin-bottom: 1rem;
		font-family: 'Bitcount Grid Single', monospace;
	}

	.modal-body {
		font-size: 1rem;
		line-height: 1.7;
		font-family: 'Courier New', Courier, monospace;
	}

	.modal-body p {
		margin-bottom: 1.25rem;
	}

	.modal-body ul {
		margin: 1rem 0 1.5rem 1.5rem;
		padding: 0;
	}

	.modal-body li {
		margin-bottom: 0.75rem;
		line-height: 1.6;
	}

	.modal-body strong {
		color: #e13737;
		font-weight: 600;
	}

	/* Scrollbar styling for modal */
	.modal-content::-webkit-scrollbar {
		width: 8px;
	}

	.modal-content::-webkit-scrollbar-track {
		background: rgba(162, 153, 139, 0.1);
		border-radius: 4px;
	}

	.modal-content::-webkit-scrollbar-thumb {
		background: #A2998B;
		border-radius: 4px;
	}

	.modal-content::-webkit-scrollbar-thumb:hover {
		background: #e13737;
	}

	/* Responsive design */
	@media (max-width: 768px) {
		.info-button {
			width: 45px;
			height: 45px;
			font-size: 24px;
			bottom: 15px;
			right: 15px;
		}

		.modal-content {
			padding: 2rem;
			max-height: 90vh;
		}

		.modal-content h2 {
			font-size: 2rem;
		}

		.modal-content h3 {
			font-size: 1.5rem;
		}

		.modal-body {
			font-size: 0.95rem;
		}
	}
</style>
