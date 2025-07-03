<script>
	import { onMount } from 'svelte';

	const rows = 8;
	const cols = 8;
	const mineCount = 10;

	let board = [];
	let gameOver = false;

	onMount(() => {
		initBoard();
	});

	function initBoard() {
		gameOver = false;
		board = Array.from({ length: rows }, () =>
			Array.from({ length: cols }, () => ({
				revealed: false,
				mine: false,
				adjacent: 0
			}))
		);

		placeMines();
		calculateAdjacents();
	}

	function placeMines() {
		let placed = 0;
		while (placed < mineCount) {
			const r = Math.floor(Math.random() * rows);
			const c = Math.floor(Math.random() * cols);
			if (!board[r][c].mine) {
				board[r][c].mine = true;
				placed++;
			}
		}
	}

	function calculateAdjacents() {
		for (let r = 0; r < rows; r++) {
			for (let c = 0; c < cols; c++) {
				if (board[r][c].mine) continue;
				let count = 0;
				for (let dr = -1; dr <= 1; dr++) {
					for (let dc = -1; dc <= 1; dc++) {
						const nr = r + dr;
						const nc = c + dc;
						if (nr >= 0 && nr < rows && nc >= 0 && nc < cols && board[nr][nc].mine) {
							count++;
						}
					}
				}
				board[r][c].adjacent = count;
			}
		}
	}

	function reveal(r, c) {
		if (gameOver || board[r][c].revealed) return;

		board[r][c].revealed = true;

		if (board[r][c].mine) {
			gameOver = true;
			revealAll();
			return;
		}

		if (board[r][c].adjacent === 0) {
			for (let dr = -1; dr <= 1; dr++) {
				for (let dc = -1; dc <= 1; dc++) {
					const nr = r + dr;
					const nc = c + dc;
					if (nr >= 0 && nr < rows && nc >= 0 && nc < cols) {
						reveal(nr, nc);
					}
				}
			}
		}
	}

	function revealAll() {
		for (let r = 0; r < rows; r++) {
			for (let c = 0; c < cols; c++) {
				board[r][c].revealed = true;
			}
		}
	}

	function restart() {
		initBoard();
	}
</script>

<main>
	<h2 class="heading">💣 Minesweeper</h2>

	<div class="grid">
		{#each board as row, r}
			{#each row as cell, c}
				<div
					class="cell {cell.revealed ? 'revealed' : ''} {cell.mine && cell.revealed ? 'mine' : ''}"
					on:click={() => reveal(r, c)}
				>
					{#if cell.revealed}
						{#if cell.mine}
							💣
						{:else if cell.adjacent > 0}
							{cell.adjacent}
						{/if}
					{/if}
				</div>
			{/each}
		{/each}
	</div>

	{#if gameOver}
		<p class="status">💥 Game Over!</p>
	{/if}

	<button class="restart" on:click={restart}>🔄 Restart</button>
</main>

<style>
	main {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
		font-family: 'Segoe UI', sans-serif;
		background-color: #121212;
		color: #f3f3f3;
		padding: 1rem;
	}

	.heading {
		color: #facc15;
		margin-bottom: 1rem;
		font-size: 2rem;
	}

	.grid {
		display: grid;
		grid-template-columns: repeat(8, 45px);
		gap: 6px;
	}

	.cell {
		width: 45px;
		height: 45px;
		background-color: #2d2d2d;
		border: 2px solid #444;
		border-radius: 6px;
		display: flex;
		align-items: center;
		justify-content: center;
		font-size: 1.2rem;
		cursor: pointer;
		user-select: none;
		transition: background-color 0.3s;
	}

	.cell:hover {
		background-color: #3a3a3a;
	}

	.cell.revealed {
		background-color: #3b3b3b;
		border-color: #666;
		cursor: default;
	}

	.cell.mine {
		background-color: #b91c1c;
		color: white;
	}

	.status {
		margin-top: 1rem;
		font-size: 1.3rem;
		color: #f87171;
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

	/* Medium Screens */
	@media (max-width: 600px) {
		.grid {
			grid-template-columns: repeat(8, 38px);
			gap: 5px;
		}

		.cell {
			width: 38px;
			height: 38px;
			font-size: 1rem;
		}
	}

	/* Small Screens */
	@media (max-width: 400px) {
		.grid {
			grid-template-columns: repeat(8, 32px);
			gap: 4px;
		}

		.cell {
			width: 32px;
			height: 32px;
			font-size: 0.9rem;
		}
	}
</style>
