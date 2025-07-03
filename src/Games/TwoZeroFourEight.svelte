<script>
	import { onMount } from "svelte";
	import { writable, get } from "svelte/store"; // ✅ FIX: import get()

	const size = 4;
	let board = writable(createEmptyBoard());
	let score = writable(0);
	let gameOver = writable(false);
	let won = writable(false);

	function createEmptyBoard() {
		return Array(size).fill(null).map(() => Array(size).fill(0));
	}

	function getEmptyCells(board) {
		let emptyCells = [];
		board.forEach((row, r) =>
			row.forEach((cell, c) => {
				if (cell === 0) emptyCells.push({ r, c });
			})
		);
		return emptyCells;
	}

	function addRandomTile(board) {
		let emptyCells = getEmptyCells(board);
		if (emptyCells.length === 0) return board;
		let { r, c } = emptyCells[Math.floor(Math.random() * emptyCells.length)];
		board[r][c] = Math.random() > 0.9 ? 4 : 2;
		return board;
	}

	function move(board, direction) {
		let newBoard = JSON.parse(JSON.stringify(board));
		let changed = false;

		function slide(row) {
			let filtered = row.filter(n => n);
			for (let i = 0; i < filtered.length - 1; i++) {
				if (filtered[i] === filtered[i + 1]) {
					filtered[i] *= 2;
					filtered[i + 1] = 0;
					score.update(s => s + filtered[i]);
					if (filtered[i] === 2048) won.set(true);
					changed = true;
				}
			}
			return filtered.filter(n => n).concat(Array(size - filtered.filter(n => n).length).fill(0));
		}

		for (let i = 0; i < size; i++) {
			let original;
			if (direction === "left" || direction === "right") {
				original = [...newBoard[i]];
				let row = direction === "right" ? newBoard[i].slice().reverse() : newBoard[i];
				let newRow = slide(row);
				newBoard[i] = direction === "right" ? newRow.reverse() : newRow;
				if (JSON.stringify(original) !== JSON.stringify(newBoard[i])) changed = true;
			} else {
				original = newBoard.map(row => row[i]);
				let col = direction === "down" ? original.slice().reverse() : original;
				let newCol = slide(col);
				newCol = direction === "down" ? newCol.reverse() : newCol;
				newCol.forEach((val, r) => (newBoard[r][i] = val));
				if (JSON.stringify(original) !== JSON.stringify(newCol)) changed = true;
			}
		}
		return changed ? addRandomTile(newBoard) : board;
	}

    function handleKey(event) {
        const keyToDir = {
            ArrowLeft: "left",
            ArrowRight: "right",
            ArrowUp: "up",
            ArrowDown: "down"
        };

        if (!keyToDir[event.key]) return;

        event.preventDefault(); // ✅ Prevent page scroll on arrow keys

        if (get(gameOver)) return;

        board.update(b => move(b, keyToDir[event.key]));
        checkGameOver();
    }


	function checkGameOver() {
		let b = get(board); // ✅ FIXED

		if (getEmptyCells(b).length > 0) return gameOver.set(false);

		for (let r = 0; r < size; r++) {
			for (let c = 0; c < size; c++) {
				if ((c < size - 1 && b[r][c] === b[r][c + 1]) || (r < size - 1 && b[r][c] === b[r + 1][c])) {
					return gameOver.set(false);
				}
			}
		}
		gameOver.set(true);
	}

	function restart() {
		board.set(addRandomTile(createEmptyBoard()));
		score.set(0);
		gameOver.set(false);
		won.set(false);
	}

	onMount(() => {
		board.set(addRandomTile(createEmptyBoard()));
		window.addEventListener("keydown", handleKey);
		return () => window.removeEventListener("keydown", handleKey);
	});
</script>

<main>
	<h1>2048</h1>
	<p class="score">Score: {$score}</p>

	<div class="board">
		{#each $board as row}
			{#each row as cell}
				<div class="tile tile-{cell}">
					{cell > 0 ? cell : ''}
				</div>
			{/each}
		{/each}
	</div>

	<button on:click={restart}>🔄 Restart</button>

	{#if $won || $gameOver}
		<div class="overlay">
			<div class="popup">
				<p>{$won ? '🎉 You Win!' : '💀 Game Over'}</p>
				<button on:click={restart}>Play Again</button>
			</div>
		</div>
	{/if}
</main>

<style>
	main {
		display: flex;
		flex-direction: column;
		align-items: center;
		padding: 2rem;
		background: #121212;
		color: #f3f3f3;
		min-height: 100vh;
	}

	h1 {
		font-size: 3rem;
		color: #facc15;
		margin-bottom: 0.5rem;
	}

	.score {
		font-size: 1.2rem;
		color: #a3e635;
		margin-bottom: 1.5rem;
	}

	.board {
		display: grid;
		grid-template-columns: repeat(4, 80px);
		grid-gap: 10px;
		padding: 10px;
		background-color: #2e2e2e;
		border-radius: 12px;
	}

	.tile {
		width: 80px;
		height: 80px;
		border-radius: 8px;
		display: flex;
		align-items: center;
		justify-content: center;
		font-size: 1.5rem;
		font-weight: bold;
		background-color: #3d3d3d;
		color: #f3f3f3;
		transition: background-color 0.2s;
	}

	/* Tile colors */
	.tile-0 { background: #3d3d3d; }
	.tile-2 { background: #e0dede; color: #333; }
	.tile-4 { background: #ede0c8; color: #333; }
	.tile-8 { background: #f2b179; }
	.tile-16 { background: #f59563; }
	.tile-32 { background: #f67c5f; }
	.tile-64 { background: #f65e3b; }
	.tile-128 { background: #edcf72; color: #fff; }
	.tile-256 { background: #edcc61; color: #fff; }
	.tile-512 { background: #edc850; color: #fff; }
	.tile-1024 { background: #edc53f; color: #fff; }
	.tile-2048 { background: #edc22e; color: #fff; }

	button {
		margin-top: 1.5rem;
		padding: 0.6rem 1.2rem;
		font-size: 1rem;
		background: #4f46e5;
		color: white;
		border: none;
		border-radius: 8px;
		cursor: pointer;
		transition: background 0.3s;
	}

	button:hover {
		background: #3730a3;
	}

	.overlay {
		position: fixed;
		top: 0; left: 0; right: 0; bottom: 0;
		background: rgba(0, 0, 0, 0.7);
		display: flex;
		justify-content: center;
		align-items: center;
	}

	.popup {
		background: #1f1f1f;
		padding: 20px;
		border-radius: 12px;
		text-align: center;
		box-shadow: 0 0 15px rgba(0,0,0,0.5);
	}

	.popup p {
		font-size: 1.5rem;
		margin-bottom: 1rem;
		color: #facc15;
	}

	/* Existing styles remain unchanged above 400px */

	@media (max-width: 399px) {
		.board {
			grid-template-columns: repeat(4, 60px);
			grid-gap: 6px;
			padding: 6px;
		}

		.tile {
			width: 60px;
			height: 60px;
			font-size: 1.2rem;
		}

		h1 {
			font-size: 2rem;
			margin-bottom: 0.5rem;
		}

		.score {
			font-size: 1rem;
			margin-bottom: 1rem;
		}

		button {
			margin-top: 1rem;
			padding: 0.5rem 1rem;
			font-size: 0.9rem;
		}

		.popup p {
			font-size: 1.2rem;
		}

		.popup {
			padding: 16px;
		}
	}

</style>
