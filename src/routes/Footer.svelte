<script lang="ts">
    import GameOfLife from "$lib/GameOfLife.svelte";
</script>

<section id="footer" class="footer-section">

	<div class="footer-content">
		<h2>Contact Us</h2>
		<p>
			Interested in autonomous systems or want to join our team?<br />
			<a href="mailto:cornellautonomousmotions@gmail.com" class="email-link">
				cornellautonomousmotions@gmail.com
			</a>
		</p>
	</div>

	<div class="footer-gol">
		<div class="fade-top"></div>

		<GameOfLife
			height="50vh"
			showCAMText={false}
			enableInteractions={true}
		/>

		<div class="fade-bottom"></div>
	</div>

	<div class="footer-bottom">
		<p>&copy; {new Date().getFullYear()} Cornell Autonomous Motions</p>
	</div>

</section>



<style>
.footer-section {
	display: flex;
	flex-direction: column;
	width: 100%;
	color: #a2998b;
	padding: 4rem 2rem 0rem 2rem;
	gap: 3rem;
	background: transparent;
}

/* -------------------------
   TEXT (left aligned)
------------------------- */
.footer-content {
	max-width: 800px;
	width: 100%;
	margin: 0 auto;
	text-align: left;

	/* NEW: overlap downward */
	margin-bottom: -3rem;   /* adjust amount */
	z-index: 20;            /* stays above fades */
	position: relative;     /* enables z-index */
}

.footer-content h2 {
	color: #e13737;
	font-size: 5rem;
	margin: 0 0 1rem;
	font-family: "Bitcount Grid Single", monospace;
}

.footer-content p {
	font-size: 1.125rem;
	font-family: monospace;
	line-height: 1.6;
}

.email-link {
	color: #e13737;
	text-decoration: underline;
	font-family: monospace;
}

/* -------------------------
   GAME OF LIFE SECTION
   with top + bottom fades
------------------------- */
.footer-gol {
	position: relative;
	width: 100%;
	height: 50vh;
	overflow: hidden;
}

/* Game of Life canvas covers full area */
.footer-gol :global(canvas) {
	width: 100%;
	height: 100%;
	object-fit: cover;
}

/* Upper fade (overlaps top) */
.fade-top {
	position: absolute;
	top: 0;
	left: 0;
	width: 100%;
	height: 50%;
	background: linear-gradient(
		to bottom,
		var(--color-bg-game),
		rgb(from var(--color-bg-game) r g b / 0.7),
		transparent
	);
	pointer-events: none;
	z-index: 10;
}

/* Lower fade (overlaps bottom) */
.fade-bottom {
	position: absolute;
	bottom: 0;
	left: 0;
	width: 100%;
	height: 10%;
	background: linear-gradient(
		to top,
		var(--color-bg-game),
		rgb(from var(--color-bg-game) r g b / 0.7),
		transparent
	);
	pointer-events: none;
	z-index: 10;
}

/* -------------------------
   COPYRIGHT
------------------------- */
.footer-bottom {
	width: 100%;
	text-align: center;
}

.footer-bottom p {
	font-size: 0.9rem;
	color: rgba(162, 153, 139, 0.6);
	font-family: monospace;
	margin: 0;
}

/* -------------------------
   RESPONSIVE
------------------------- */
@media (max-width: 768px) {
	.footer-content h2 {
		font-size: 2.2rem;
	}
	.footer-content p {
		font-size: 1rem;
	}
}

</style>
