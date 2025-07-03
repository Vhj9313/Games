<script>
	import { onMount } from 'svelte';
	import { writable, get } from 'svelte/store';

	const board = writable([]);
	const initialBoard = writable([]);
	const solutionBoard = writable([]);

	let inputStatus = Array.from({ length: 9 }, () => Array(9).fill('')); // '', 'valid', or 'invalid'

	onMount(() => {
		generateSudoku();
	});

	function generateSudoku() {
		const newBoard = Array.from({ length: 9 }, () => Array(9).fill(0));
		const newSolution = Array.from({ length: 9 }, () => Array(9).fill(0));
		fillBoard(newBoard, newSolution);
		removeNumbers(newBoard);
		board.set(newBoard);
		initialBoard.set(newBoard.map(row => [...row]));
		solutionBoard.set(newSolution);
		inputStatus = Array.from({ length: 9 }, () => Array(9).fill(''));
	}

	function fillBoard(board, solution, row = 0, col = 0) {
		if (row === 9) return true;
		if (col === 9) return fillBoard(board, solution, row + 1, 0);
		if (board[row][col] !== 0) return fillBoard(board, solution, row, col + 1);

		const numbers = shuffle([1, 2, 3, 4, 5, 6, 7, 8, 9]);
		for (let num of numbers) {
			if (isValidMove(board, row, col, num)) {
				board[row][col] = num;
				solution[row][col] = num;
				if (fillBoard(board, solution, row, col + 1)) return true;
				board[row][col] = 0;
				solution[row][col] = 0;
			}
		}
		return false;
	}

	function removeNumbers(board, maxRemoved = 40) {
		let removed = 0;
		while (removed < maxRemoved) {
			const row = Math.floor(Math.random() * 9);
			const col = Math.floor(Math.random() * 9);
			if (board[row][col] !== 0) {
				board[row][col] = 0;
				removed++;
			}
		}
	}

	function shuffle(array) {
		for (let i = array.length - 1; i > 0; i--) {
			const j = Math.floor(Math.random() * (i + 1));
			[array[i], array[j]] = [array[j], array[i]];
		}
		return array;
	}

	function isValidMove(board, row, col, value) {
		return (
			!board[row].includes(value) &&
			!board.map(r => r[col]).includes(value) &&
			!getBox(board, row, col).includes(value)
		);
	}

	function getBox(board, row, col) {
		const startRow = Math.floor(row / 3) * 3;
		const startCol = Math.floor(col / 3) * 3;
		const box = [];
		for (let i = startRow; i < startRow + 3; i++) {
			for (let j = startCol; j < startCol + 3; j++) {
				box.push(board[i][j]);
			}
		}
		return box;
	}

	function handleInput(event, row, col) {
		const raw = event.target.value.trim();
		const value = parseInt(raw);

		if (!/^[1-9]$/.test(raw)) {
			event.target.value = '';
			inputStatus[row][col] = '';
			return;
		}

		board.update(current => {
			current[row][col] = value;

			const sol = get(solutionBoard);
			if (sol[row][col] === value) {
				inputStatus[row][col] = 'valid';
			} else {
				inputStatus[row][col] = 'invalid';
			}

			return current;
		});
	}

	function resetBoard() {
		generateSudoku();
	}
</script>

<main>
	<h2 class="heading">🧩 Sudoku</h2>

	<div class="board">
		{#each $board as row, rowIndex}
			{#each row as cell, colIndex}
				<div
					class="cell
						{colIndex === 2 || colIndex === 5 ? 'bold-right' : ''}
						{rowIndex === 2 || rowIndex === 5 ? 'bold-bottom' : ''}
						{$initialBoard[rowIndex][colIndex] !== 0 ? 'initial' : ''}">
					{#if $initialBoard[rowIndex][colIndex] === 0}
						<input
							type="text"
							maxlength="1"
							class="{inputStatus[rowIndex][colIndex]}"
							on:input={(e) => handleInput(e, rowIndex, colIndex)}
							inputmode="numeric"
							pattern="[1-9]" />
					{:else}
						<span class="fixed">{$initialBoard[rowIndex][colIndex]}</span>
					{/if}
				</div>
			{/each}
		{/each}
	</div>

	<button class="reset-button" on:click={resetBoard}>♻️ New Game</button>
</main>

<style>
	main {
		text-align: center;
		padding: 1rem;
		background-color: #1e1e1e;
		color: #f3f3f3;
		font-family: 'Segoe UI', sans-serif;
	}

	.heading {
		color: #facc15;
		margin-bottom: 1rem;
	}

	.board {
		display: grid;
		grid-template-columns: repeat(9, 40px);
		margin: 0 auto;
		width: fit-content;
		border: 2px solid #444;
	}

	.cell {
		width: 40px;
		height: 40px;
		border: 1px solid #555;
		display: flex;
		align-items: center;
		justify-content: center;
		box-sizing: border-box;
		background-color: #2a2a2a;
	}

	.cell input {
		width: 100%;
		height: 100%;
		border: none;
		outline: none;
		background: transparent;
		text-align: center;
		font-size: 1.5rem;
		color: #f3f3f3;
	}

	.cell.initial {
		background-color: #3a3a3a;
		color: #ccc;
	}

	.bold-right {
		border-right: 2px solid #777;
	}

	.bold-bottom {
		border-bottom: 2px solid #777;
	}

	.fixed {
		pointer-events: none;
	}

	input.valid {
		background-color: #15803d !important; /* green */
		color: white;
	}

	input.invalid {
		background-color: #b91c1c !important; /* red */
		color: white;
	}

	.reset-button {
		margin-top: 1rem;
		padding: 0.6rem 1.2rem;
		font-size: 1rem;
		background-color: #4f46e5;
		color: white;
		border: none;
		border-radius: 6px;
		cursor: pointer;
	}

	.reset-button:hover {
		background-color: #3730a3;
	}

	/* Existing styles remain unchanged above 400px */

	@media (max-width: 399px) {
		.board {
			grid-template-columns: repeat(9, 28px); /* Smaller cells */
			gap: 2px;
			border-width: 1px;
		}

		.cell {
			width: 28px;
			height: 28px;
			font-size: 1.2rem;
			border-width: 1px;
		}

		.cell input {
			font-size: 1rem;
		}

		.heading {
			font-size: 1.2rem;
			margin-bottom: 0.8rem;
		}

		.reset-button {
			margin-top: 1rem;
			padding: 0.5rem 1rem;
			font-size: 0.85rem;
		}

		.bold-right {
			border-right: 1.5px solid #777;
		}

		.bold-bottom {
			border-bottom: 1.5px solid #777;
		}
	}

</style>
