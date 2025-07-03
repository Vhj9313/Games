<footer class="footer">
	<div class="footer-content">
		<p>
			&copy; {new Date().getFullYear()} <span class="name">Game Hub</span>. All rights reserved.
		</p>
		<p class="credit">Built with ❤️ using Svelte</p>
	</div>
</footer>

<style>
	.footer {
		bottom: 0;
		left: 0;
		width: 100%;
		background-color: #1e1e1e;
		color: #cccccc;
		padding: 1.2rem 1rem;
		font-size: 0.95rem;
		text-align: center;
		border-top: 2px solid transparent;
		border-image: linear-gradient(to right, #4f46e5, #9333ea) 1;
		box-shadow: 0 -2px 10px rgba(0, 0, 0, 0.4);
		z-index: 9999;
	}

	.footer-content {
		max-width: 1000px;
		margin: 0 auto;
	}

	.name {
		color: #4f46e5;
		font-weight: bold;
	}

	.credit {
		margin-top: 0.3rem;
		font-size: 0.85rem;
		color: #aaaaaa;
	}

	@media (max-width: 600px) {
		.footer {
			font-size: 0.85rem;
			padding: 1rem;
		}
	}
</style>
