<script>
	import SnakeGame from './Games/SnakeGame.svelte';
	import TicTackToe from './Games/TicTackToe.svelte';
	// import SudokuNonErasable from './Games/SudokuNonErasable.svelte';
	import Sudoku from './Games/Sudoku.svelte';
	import TwoZeroFourEight from './Games/TwoZeroFourEight.svelte';
	import MemoryMatch from './Games/MemoryMatch.svelte';
	import Minesweeper from './Games/Minesweeper.svelte';
	import WhackAMole from './Games/WhackAMole.svelte';
	import RockPaperScissors from './Games/RockPaperScissors.svelte';

	let selectedGame = null;

	const games = [
		{ name: 'Snake Game', emoji: '🐍', component: SnakeGame },
		{ name: 'Tic Tac Toe', emoji: '❌⭕', component: TicTackToe },
		// { name: 'Sudoku (Non-Erasable)', emoji: '🧩', component: SudokuNonErasable },
		{ name: 'Sudoku', emoji: '✏️🧩', component: Sudoku },
		{ name: '2048 Game', emoji: '🔢', component: TwoZeroFourEight },
		{ name: 'Memory Match', emoji: '🧠', component: MemoryMatch },
		{ name: 'Minesweeper', emoji: '💣', component: Minesweeper },
		{ name: 'Whack-a-Mole', emoji: '🐹', component: WhackAMole },
		{ name: 'Rock Paper Scissors', emoji: '✊✋✌️', component: RockPaperScissors },
	];

	function selectGame(game) {
		selectedGame = game;
	}
</script>

<main>
	<h1>🎮 Game's Hub</h1>

	{#if !selectedGame}
		<div class="grid">
			{#each games as game}
				<div class="card" on:click={() => selectGame(game)}>
					<div class="emoji">{game.emoji}</div>
					<div class="title">{game.name}</div>
				</div>
			{/each}
		</div>
	{:else}
		<div class="game-container">
			<button class="back-btn" on:click={() => selectedGame = null}>←</button>
			<div class="game-box animate-in">
				<svelte:component this={selectedGame.component} />
			</div>
		</div>
	{/if}
</main>

<style>
	:root {
		--bg-dark: #121212;
		--card-bg: #1e1e1e;
		--text-light: #ffffff;
		--highlight: #4f46e5;
	}

	main {
		text-align: center;
		width: 100%;
		margin: 0 auto;
		color: var(--text-light);
	}

	h1 {
		font-size: 2.5rem;
		margin-bottom: 2rem;
	}

	.grid {
		display: grid;
		grid-template-columns: repeat(auto-fit, minmax(180px, 1fr));
		gap: 1.5rem;
		width: 100%;
	}

	.card {
		background-color: var(--card-bg);
		padding: 1.5rem;
		border-radius: 1rem;
		box-shadow: 0 4px 10px rgba(0, 0, 0, 0.3);
		cursor: pointer;
		transition: transform 0.2s, background-color 0.2s;
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
	}

	.card:hover {
		transform: scale(1.05);
		background-color: var(--highlight);
	}

	.emoji {
		font-size: 2.5rem;
		margin-bottom: 0.5rem;
	}

	.title {
		font-size: 1rem;
		font-weight: 600;
		text-align: center;
	}

	.game-container {
		display: flex;
		flex-direction: column;
		align-items: center;
		width: 100%;
	}

	.back-btn {
		align-self: flex-start;
		margin-bottom: 0.5rem;
		padding: 0.6rem 1.2rem;
		font-size: 1rem;
		border: none;
		border-radius: 10px;
		background: linear-gradient(to right, #4f46e5, #9333ea);
		color: white;
		cursor: pointer;
		transition: background 0.3s, transform 0.2s;
		font-weight: 500;
	}

	.back-btn:hover {
		background: linear-gradient(to right, #4338ca, #7e22ce);
		transform: scale(1.02);
	}

	.animate-in {
		animation: fadeInUp 0.4s ease-out;
	}

	@keyframes fadeInUp {
		from {
			opacity: 0;
			transform: translateY(20px);
		}
		to {
			opacity: 1;
			transform: translateY(0);
		}
	}
</style>
