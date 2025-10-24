<script lang="ts">
	import GameOfLife from '$lib/GameOfLife.svelte';
</script>

<section id="footer" class="footer-section">
	<div class="bottom-game-of-life">
		<GameOfLife height="50vh" showCAMText={false} enableInteractions={true} />
		<div class="bottom-fade-overlay"></div>
	</div>
	<div class="footer-content">
		<h2>Contact Us</h2>
		<p>
			Interested in autonomous systems or want to join our team?<br>
			<a href="mailto:cornellautonomousmotions@gmail.com" class="email-link">
				cornellautonomousmotions@gmail.com
			</a>
		</p>
	</div>
	<div class="footer-bottom">
		<p>&copy; {new Date().getFullYear()} Cornell Autonomous Motions</p>
	</div>
</section>

<style>
	.footer-section {
		position: relative;
		background-color: transparent;
		color: #A2998B;
		padding: 4rem 2rem 0;
		z-index: 2;
		height: 60vh;
		display: flex;
		flex-direction: column;
		justify-content: space-between;
	}

	.bottom-game-of-life {
		position: absolute;
		bottom: 0;
		left: 0;
		width: 100%;
		height: 35vh;
		z-index: 0;
		pointer-events: none;
	}

	.bottom-fade-overlay {
		position: absolute;
		top: 0;
		left: 0;
		width: 100%;
		height: 100%;
		background: linear-gradient(to top, 
			transparent 0%,
			transparent 30%,
			rgb(from var(--color-bg-game) r g b / 0.3) 55%,
			rgb(from var(--color-bg-game) r g b / 0.6) 70%,
			rgb(from var(--color-bg-game) r g b / 0.85) 85%,
			var(--color-bg-game) 100%);
		pointer-events: none;
		z-index: 1;
	}

	.footer-content {
		position: relative;
		max-width: 800px;
		margin: 0 auto;
		z-index: 10;
		width: 100%;
	}

	.footer-content h2 {
		color: #e13737;
		font-size: 5rem;
		margin-top: 0;
		margin-bottom: 1rem;
		font-family: 'Bitcount Grid Single', monospace;
	}

	.footer-content p {
		font-size: 1.125rem;
		line-height: 1.6;
		margin-bottom: 1.5rem;
		font-family: monospace;
	}

	.email-link {
		color: #e13737;
		text-decoration: underline;
		font-family: monospace;
		transition: opacity 0.2s ease;
	}

	.email-link:hover {
		opacity: 0.7;
	}

	.footer-bottom {
		position: relative;
		z-index: 10;
		text-align: center;
		padding-bottom: 0.5rem;
		margin-top: auto;
	}

	.footer-bottom p {
		font-size: 0.875rem;
		color: rgba(162, 153, 139, 0.6);
		margin: 0;
		font-family: monospace;
	}

	/* Responsive design */
	@media (max-width: 768px) {
		.footer-content h2 {
			font-size: 2rem;
		}
	}
</style>
