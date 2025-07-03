<nav class="navbar">
	<div class="marquee">
		<div class="marquee-content">
			<!-- Repeat logo text multiple times for continuous effect -->
			{#each Array(20) as _, i}
				<div class="logo" aria-hidden="true">
					<span class="emoji">🎮</span>
					<span class="title">Game<span class="accent">Hub</span></span>
				</div>
			{/each}
		</div>
	</div>
</nav>

<style>
	.navbar {
		width: 100%;
		background: linear-gradient(to right, #1e1e1e, #121212);
		box-shadow: 0 4px 12px rgba(0, 0, 0, 0.4);
		overflow: hidden;
		height: 64px;
		position: sticky;
		top: 0;
		z-index: 100;
	}

	.marquee {
		white-space: nowrap;
		overflow: hidden;
		width: 100%;
		height: 100%;
		display: flex;
		align-items: center;
	}

	.marquee-content {
		display: flex;
		animation: scroll-left 20s linear infinite;
		gap: 2rem;
	}

	.logo {
		display: flex;
		align-items: center;
		gap: 0.5rem;
		font-size: 1.5rem;
		font-weight: bold;
		color: white;
		white-space: nowrap;
	}

	.emoji {
		font-size: 1.8rem;
	}

	.title {
		color: white;
	}

	.accent {
		color: #4f46e5;
	}

	@keyframes scroll-left {
		from {
			transform: translateX(0%);
		}
		to {
			transform: translateX(-50%);
		}
	}
</style>
