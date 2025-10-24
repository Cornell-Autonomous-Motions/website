<section id="footer" class="footer-section">
	<div class="footer-content">
		<h3>Get In Touch</h3>
		<p>Interested in autonomous systems or want to join our team?</p>
		<a href="mailto:cornellautonomousmotions@gmail.com" class="email-link">
			cornellautonomousmotions@gmail.com
		</a>
		<div class="footer-bottom">
			<p>&copy; {new Date().getFullYear()} Cornell Autonomous Motions</p>
		</div>
	</div>
</section>

<style>
	.footer-section {
		position: relative;
		background-color: var(--color-bg-game);
		color: #A2998B;
		padding: 4rem 2rem 2rem;
		z-index: 2;
		border-top: 2px solid rgba(162, 153, 139, 0.2);
	}

	.footer-content {
		max-width: 800px;
		margin: 0 auto;
		text-align: center;
	}

	.footer-content h3 {
		color: #e13737;
		font-size: 2.5rem;
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
		display: inline-block;
		color: #e13737;
		text-decoration: none;
		font-size: 1.25rem;
		font-family: monospace;
		padding: 0.75rem 1.5rem;
		border: 2px solid #e13737;
		border-radius: 8px;
		transition: all 0.3s ease;
		margin-bottom: 2rem;
	}

	.email-link:hover {
		background-color: #e13737;
		color: var(--color-bg-game);
		transform: translateY(-2px);
		box-shadow: 0 4px 12px rgba(225, 55, 55, 0.3);
	}

	.footer-bottom {
		margin-top: 2rem;
		padding-top: 1.5rem;
		border-top: 1px solid rgba(162, 153, 139, 0.2);
	}

	.footer-bottom p {
		font-size: 0.875rem;
		color: rgba(162, 153, 139, 0.6);
		margin: 0;
	}

	/* Responsive design */
	@media (max-width: 768px) {
		.footer-content h3 {
			font-size: 2rem;
		}

		.email-link {
			font-size: 1rem;
			padding: 0.5rem 1rem;
		}
	}
</style>
