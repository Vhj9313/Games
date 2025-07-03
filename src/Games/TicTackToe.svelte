<script>
	let board = Array(9).fill(null);
	let currentPlayer = 'X';
	let winner = null;
	let winningCells = [];
	let vsAI = true;
	let aiThinking = false;

	const winningCombinations = [
		[0, 1, 2],
		[3, 4, 5],
		[6, 7, 8],
		[0, 3, 6],
		[1, 4, 7],
		[2, 5, 8],
		[0, 4, 8],
		[2, 4, 6]
	];

	function handleClick(index) {
		if (winner || board[index] || (vsAI && currentPlayer === 'O')) return;
		board[index] = currentPlayer;
		checkWinner();
		if (!winner) {
			currentPlayer = currentPlayer === 'X' ? 'O' : 'X';
			if (vsAI && currentPlayer === 'O') aiMove();
		}
	}

	function aiMove() {
		aiThinking = true;
		setTimeout(() => {
			const best = getBestMove();
			if (best !== -1) {
				board[best] = 'O';
				checkWinner();
				if (!winner) currentPlayer = 'X';
			}
			aiThinking = false;
		}, 400);
	}

	function getBestMove() {
		for (const combo of winningCombinations) {
			const [a, b, c] = combo;
			const values = [board[a], board[b], board[c]];
			if (values.filter(v => v === 'O').length === 2 && values.includes(null)) {
				return combo[values.indexOf(null)];
			}
		}
		for (const combo of winningCombinations) {
			const [a, b, c] = combo;
			const values = [board[a], board[b], board[c]];
			if (values.filter(v => v === 'X').length === 2 && values.includes(null)) {
				return combo[values.indexOf(null)];
			}
		}
		if (board[4] === null) return 4;
		const corners = [0, 2, 6, 8].filter(i => board[i] === null);
		if (corners.length) return corners[Math.floor(Math.random() * corners.length)];
		const empty = board.map((v, i) => v === null ? i : null).filter(v => v !== null);
		if (empty.length) return empty[Math.floor(Math.random() * empty.length)];
		return -1;
	}

	function checkWinner() {
		for (const combo of winningCombinations) {
			const [a, b, c] = combo;
			if (board[a] && board[a] === board[b] && board[a] === board[c]) {
				winner = board[a];
				winningCells = [a, b, c];
				return;
			}
		}
		if (!board.includes(null)) winner = 'draw';
	}

	function resetGame() {
		board = Array(9).fill(null);
		currentPlayer = 'X';
		winner = null;
		winningCells = [];
	}

	function toggleMode() {
		resetGame();
		vsAI = !vsAI;
	}
</script>

<main>
	<h2 class="title">❌ Tic Tac Toe ⭕</h2>

	<div class="toggle-buttons">
		<button
			class:active={!vsAI}
			on:click={() => {
				if (vsAI) toggleMode();
			}}>
			Human vs Human 👥
		</button>
		<button
			class:active={vsAI}
			on:click={() => {
				if (!vsAI) toggleMode();
			}}>
			Human vs AI 🤖
		</button>
	</div>

	<div class="status">
		{#if winner}
			{#if winner === 'draw'}
				It's a draw!
			{:else}
				Winner: {winner}
			{/if}
		{:else}
			{aiThinking ? 'AI is thinking...' : `Current Player: ${currentPlayer}`}
		{/if}
	</div>

	<div class="board">
		{#each board as cell, index}
			<div
				class="cell {winningCells.includes(index) ? 'win' : ''}"
				on:click={() => handleClick(index)}>
				{#if cell !== null}
					{cell}
				{/if}
			</div>
		{/each}
	</div>

	{#if winner}
		<button class="reset" on:click={resetGame}>🔁 Play Again</button>
	{/if}
</main>

<style>
	main {
		text-align: center;
		padding: 1rem;
		max-width: 360px;
		margin: 0 auto;
		color: #f3f3f3;
		font-family: 'Segoe UI', sans-serif;
	}

	.title {
		font-size: 2rem;
		margin-bottom: 1rem;
		color: #facc15;
	}

	.toggle-buttons {
		display: flex;
		justify-content: center;
		gap: 1rem;
		margin-bottom: 1rem;
		flex-wrap: wrap;
	}

	.toggle-buttons button {
		padding: 0.5rem 1rem;
		font-size: 0.9rem;
		border: 2px solid #4b5563;
		background-color: #1f2937;
		color: #fff;
		border-radius: 6px;
		cursor: pointer;
		transition: all 0.2s ease-in-out;
	}

	.toggle-buttons button:hover {
		background-color: #374151;
	}

	.toggle-buttons button.active {
		background-color: #4f46e5;
		border-color: #4f46e5;
		font-weight: bold;
	}

	.status {
		margin-bottom: 1rem;
		font-size: 1.2rem;
		color: #a3e635;
	}

	.board {
		display: grid;
		grid-template-columns: repeat(3, 1fr);
		gap: 8px;
	}

	.cell {
		width: 100px;
		height: 100px;
		background: #1e1e1e;
		display: flex;
		align-items: center;
		justify-content: center;
		font-size: 2.5rem;
		cursor: pointer;
		border: 2px solid #4b5563;
		border-radius: 8px;
		transition: background 0.2s ease;
	}

	.cell:hover {
		background: #2d2d2d;
	}

	.cell.win {
		background-color: #15803d;
		color: white;
	}

	.reset {
		margin-top: 1rem;
		padding: 0.6rem 1.2rem;
		font-size: 1rem;
		border: none;
		background-color: #4f46e5;
		color: white;
		border-radius: 8px;
		cursor: pointer;
		transition: background-color 0.3s;
	}

	.reset:hover {
		background-color: #4338ca;
	}

	/* ✅ Mobile responsiveness */
	@media (max-width: 480px) {
		.board {
			gap: 6px;
		}

		.cell {
			width: 70px;
			height: 70px;
			font-size: 2rem;
		}

		.toggle-buttons button {
			font-size: 0.8rem;
			padding: 0.4rem 0.8rem;
		}

		.status {
			font-size: 1rem;
		}

		.title {
			font-size: 1.5rem;
		}

		.reset {
			font-size: 0.9rem;
			padding: 0.5rem 1rem;
		}
	}
</style>

