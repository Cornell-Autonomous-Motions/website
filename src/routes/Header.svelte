<script lang="ts">
	import camLogo from '$lib/images/CAM-logo-small.png';

	function scrollToAbout(e: MouseEvent) {
		e.preventDefault();
		const aboutSection = document.getElementById('about');
		if (aboutSection) {
			aboutSection.scrollIntoView({ behavior: 'smooth' });
		}
	}

	function scrollToFooter(e: MouseEvent) {
		e.preventDefault();
		const footerSection = document.getElementById('footer');
		if (footerSection) {
			footerSection.scrollIntoView({ behavior: 'smooth' });
		}
	}
</script>

<header>
	<nav>
		<a href="/" class="logo-link">
			<img src={camLogo} alt="CAM Logo" class="logo" />
		</a>
		<div class="nav-links">
			<a href="#about" on:click={scrollToAbout}>About</a>
			<a href="https://github.com/da-luce/cam_website" target="_blank" rel="noopener noreferrer">GitHub</a>
			<a href="#footer" on:click={scrollToFooter}>Contact</a>
		</div>
	</nav>
</header>

<style>
	header {
		position: fixed;
		top: 0;
		left: 0;
		width: 100%;
		z-index: 100;
		padding: 1rem 2rem;
		background: linear-gradient(
			to bottom,
			var(--color-bg-game) 0%,
			var(--color-bg-game) 70%,
			transparent 100%
		);
	}

	nav {
		display: flex;
		justify-content: space-between;
		align-items: center;
		max-width: 1200px;
		margin: 0 auto;
	}

	.logo-link {
		display: flex;
		align-items: center;
		transition: opacity 0.2s ease;
	}

	.logo-link:hover {
		opacity: 0.7;
	}

	.logo {
		height: 40px;
		width: auto;
	}

	.nav-links {
		display: flex;
		gap: 3rem;
		align-items: center;
	}

	.nav-links a {
		color: #e13737;
		text-decoration: none;
		font-size: 1rem;
		font-family: monospace;
		transition: opacity 0.2s ease;
	}

	.nav-links a:hover {
		opacity: 0.7;
	}
</style>
