<script>
	import { onMount } from 'svelte';

	const emojis = ['🍎', '🍇', '🍓', '🍒', '🍍', '🍌', '🍉', '🥝'];
	let cards = [];
	let flippedCards = [];
	let lockBoard = false;
	let matchedPairs = 0;

	onMount(() => {
		resetGame();
	});

	function resetGame() {
		const doubledEmojis = [...emojis, ...emojis];
		const shuffled = shuffle(doubledEmojis).map((emoji, index) => ({
			id: index,
			emoji,
			flipped: false,
			matched: false
		}));
		cards = shuffled;
		flippedCards = [];
		matchedPairs = 0;
		lockBoard = false;
	}

	function shuffle(array) {
		return array
			.map(value => ({ value, sort: Math.random() }))
			.sort((a, b) => a.sort - b.sort)
			.map(({ value }) => value);
	}

	function handleCardClick(cardIndex) {
		if (lockBoard || cards[cardIndex].flipped || cards[cardIndex].matched) return;

		// Flip the card
		cards = cards.map((card, i) =>
			i === cardIndex ? { ...card, flipped: true } : card
		);

		flippedCards.push(cards[cardIndex]);

		if (flippedCards.length === 2) {
			lockBoard = true;

			if (flippedCards[0].emoji === flippedCards[1].emoji) {
				// Matched pair
				cards = cards.map(card =>
					flippedCards.some(fc => fc.id === card.id)
						? { ...card, matched: true }
						: card
				);
				matchedPairs++;
				resetFlips();
			} else {
				// Not matched, flip back after delay
				setTimeout(() => {
					cards = cards.map(card =>
						flippedCards.some(fc => fc.id === card.id)
							? { ...card, flipped: false }
							: card
					);
					resetFlips();
				}, 800);
			}
		}
	}

	function resetFlips() {
		flippedCards = [];
		lockBoard = false;
	}
</script>

<main>
	<h2 class="title">🧠 Memory Match</h2>

	<div class="grid">
		{#each cards as card, index (card.id)}
			<div
				class="card-container"
				on:click={() => handleCardClick(index)}
			>
				<div class="card {card.flipped || card.matched ? 'flipped' : ''}">
					<div class="front"></div>
					<div class="back">
						<span class="emoji">{card.emoji}</span>
					</div>
				</div>
			</div>
		{/each}
	</div>

	<p class="status">
		{matchedPairs === emojis.length
			? '🎉 You found all pairs!'
			: `Pairs matched: ${matchedPairs}/${emojis.length}`}
	</p>

	<button class="restart" on:click={resetGame}>🔄 Restart Game</button>
</main>

<style>
	main {
		text-align: center;
		padding: 1rem;
		background-color: #121212;
		color: #e5e5e5;
	}

	.title {
		font-size: 2rem;
		margin-bottom: 1rem;
		color: #facc15;
	}

	.grid {
		display: grid;
		grid-template-columns: repeat(4, 1fr);
		gap: 10px;
		max-width: 400px;
		margin: 0 auto 1rem;
	}

	.card-container {
		perspective: 1000px;
	}

	.card {
		width: 100%;
		aspect-ratio: 1 / 1;
		transform-style: preserve-3d;
		transition: transform 0.5s;
		position: relative;
		cursor: pointer;
	}

	.card.flipped {
		transform: rotateY(180deg);
	}

	.front,
	.back {
		position: absolute;
		width: 100%;
		height: 100%;
		display: flex;
		align-items: center;
		justify-content: center;
		border-radius: 8px;
		backface-visibility: hidden;
	}

	.front {
		background-color: #2d2d2d;
		border: 2px solid #444;
	}

	.back {
		background-color: #4ade80;
		border: 2px solid #16a34a;
		transform: rotateY(180deg);
	}

	.emoji {
		font-size: 2rem;
	}

	.status {
		margin-top: 1rem;
		font-size: 1.2rem;
		color: #a3e635;
	}

	.restart {
		margin-top: 1rem;
		padding: 0.6rem 1.2rem;
		font-size: 1rem;
		background-color: #4f46e5;
		color: white;
		border: none;
		border-radius: 8px;
		cursor: pointer;
		transition: background-color 0.3s;
	}

	.restart:hover {
		background-color: #3730a3;
	}
</style>
