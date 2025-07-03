<script>
    import { onMount } from 'svelte';
    import { writable } from 'svelte/store';

    // Stores for managing state
    const board = writable([]);
    const initialBoard = writable([]);
    const solutionBoard = writable([]);

    // Generate a new Sudoku puzzle when the component mounts
    onMount(() => {
        generateSudoku();
    });

    // Generate a valid Sudoku puzzle
    function generateSudoku() {
        const newBoard = Array.from({ length: 9 }, () => Array(9).fill(0));
        const newSolution = Array.from({ length: 9 }, () => Array(9).fill(0));

        fillBoard(newBoard, newSolution); // Fill the board with a valid solution
        removeNumbers(newBoard, newSolution); // Remove numbers to create the puzzle

        // Update stores
        board.set(newBoard);
        initialBoard.set(newBoard.map(row => [...row])); // Deep copy for initial state
        solutionBoard.set(newSolution);
    }

    // Fill the board with a valid Sudoku solution
    function fillBoard(board, solution, row = 0, col = 0) {
        if (row === 9) return true; // Base case: board is filled
        if (col === 9) return fillBoard(board, solution, row + 1, 0); // Move to the next row
        if (board[row][col] !== 0) return fillBoard(board, solution, row, col + 1); // Skip filled cells

        const numbers = shuffle([1, 2, 3, 4, 5, 6, 7, 8, 9]);
        for (let num of numbers) {
            if (isValidMove(board, row, col, num)) {
                board[row][col] = num;
                solution[row][col] = num;
                if (fillBoard(board, solution, row, col + 1)) return true; // Recursively fill the board
                board[row][col] = 0; // Backtrack
                solution[row][col] = 0;
            }
        }
        return false; // No valid number found
    }

    // Remove numbers to create the puzzle
    function removeNumbers(board, solution, maxRemoved = 40) {
        let numRemoved = 0;
        while (numRemoved < maxRemoved) {
            const row = Math.floor(Math.random() * 9);
            const col = Math.floor(Math.random() * 9);
            if (board[row][col] !== 0) {
                board[row][col] = 0;
                numRemoved++;
            }
        }
    }

    // Shuffle an array using the Fisher-Yates algorithm
    function shuffle(array) {
        for (let i = array.length - 1; i > 0; i--) {
            const j = Math.floor(Math.random() * (i + 1));
            [array[i], array[j]] = [array[j], array[i]];
        }
        return array;
    }

    // Check if a move is valid
    function isValidMove(board, row, col, value) {
        return (
            !board[row].includes(value) && // Check row
            !board.map(row => row[col]).includes(value) && // Check column
            !getBox(board, row, col).includes(value) // Check 3x3 box
        );
    }

    // Get the 3x3 box that a cell belongs to
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

    // Handle user input
    function handleInput(event, row, col) {
        const value = parseInt(event.target.value) || 0;
        if (value < 1 || value > 9) {
            event.target.value = ''; // Clear invalid input
            return;
        }

        solutionBoard.subscribe(solution => {
            if (value === solution[row][col]) {
                event.target.classList.add('valid');
                event.target.classList.remove('invalid');

                // Update board state
                board.update(currentBoard => {
                    currentBoard[row][col] = value;
                    return currentBoard;
                });

                // Mark cell as initial (non-editable)
                initialBoard.update(initial => {
                    initial[row][col] = value;
                    return initial;
                });
            } else {
                event.target.classList.add('invalid');
                event.target.classList.remove('valid');
            }
        });
    }



    // Reset the board to generate a new puzzle
    function resetBoard() {
        generateSudoku();
    }
</script>

<main>
    <div class="heading">
        <h3>Sudoku Non Erasable</h3>
    </div>
    <div class="board">
        {#each $board as row, rowIndex}
            {#each row as cell, colIndex}
                <div class="cell
                    {colIndex === 2 || colIndex === 5 ? 'bold-right' : ''}
                    {rowIndex === 2 || rowIndex === 5 ? 'bold-bottom' : ''}
                    {$initialBoard[rowIndex][colIndex] !== 0 ? 'initial' : ''}">
                    {#if $initialBoard[rowIndex][colIndex] === 0}
                        <input
                            type="number"
                            min="1"
                            max="9"
                            maxlength="1"
                            on:input={(event) => handleInput(event, rowIndex, colIndex)}
                        />
                    {:else}
                        {$initialBoard[rowIndex][colIndex]}
                    {/if}
                </div>
            {/each}
        {/each}
    </div>

    <button class="reset-button" on:click={resetBoard}>New Game</button>
</main>

<style>
    .heading {
        text-align: center;
    }
    .board {
        display: grid;
        grid-template-columns: repeat(9, 40px);
        width: 360px;
        margin: 0 auto;
        border: 2px solid #333;
    }
    .cell {
        width: 40px;
        height: 40px;
        text-align: center;
        font-size: 1.5rem;
        border: 1px solid #ccc;
        box-sizing: border-box;
    }
    .cell input {
        width: 100%;
        height: 100%;
        text-align: center;
        font-size: 1.5rem;
        border: none;
    }
    .cell.initial {
        background-color: #f0f0f0;
    }
    .reset-button {
        display: block;
        margin: 20px auto;
        padding: 10px 20px;
        font-size: 1rem;
        cursor: pointer;
    }
    .cell.bold-right {
        border-right: 2px solid #333;
    }
    .cell.bold-bottom {
        border-bottom: 2px solid #333;
    }
    .valid {
        background-color: #d4edda; /* Light green for correct input */
    }
    .invalid {
        background-color: #f8d7da; /* Light red for incorrect input */
    }
</style>