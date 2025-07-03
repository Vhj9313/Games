<script>
    import { onMount } from 'svelte';

    const ROWS = 6;
    const COLS = 7;
    const EMPTY = null;

    // Initialize board at declaration
    let board = Array.from({ length: ROWS }, () => Array(COLS).fill(EMPTY));
    let currentPlayer = '🔴';
    let winner = null;
    let isVsAI = false;
    let gameOver = false;

    function initBoard() {
        board = Array.from({ length: ROWS }, () => Array(COLS).fill(EMPTY));
        currentPlayer = '🔴';
        winner = null;
        gameOver = false;
    }

    onMount(() => {
        initBoard();
    });

    function toggleMode() {
        isVsAI = !isVsAI;
        resetGame();
    }

    function resetGame() {
        initBoard();
    }

    function handleClick(col) {
        if (gameOver) return;

        for (let row = ROWS - 1; row >= 0; row--) {
            if (!board[row][col]) {
                board[row][col] = currentPlayer;
                if (checkWinner(row, col, currentPlayer)) {
                    winner = currentPlayer;
                    gameOver = true;
                } else {
                    currentPlayer = currentPlayer === '🔴' ? '🟡' : '🔴';
                    if (isVsAI && currentPlayer === '🟡' && !gameOver) {
                        setTimeout(aiMove, 400);
                    }
                }
                break;
            }
        }
    }

    function aiMove() {
        // Simple AI: pick first available column
        for (let col = 0; col < COLS; col++) {
            for (let row = ROWS - 1; row >= 0; row--) {
                if (!board[row][col]) {
                    handleClick(col);
                    return;
                }
            }
        }
    }

    function checkWinner(row, col, player) {
        return (
            countConnected(row, col, 0, 1, player) + countConnected(row, col, 0, -1, player) > 2 || // Horizontal
            countConnected(row, col, 1, 0, player) > 2 || // Vertical
            countConnected(row, col, 1, 1, player) + countConnected(row, col, -1, -1, player) > 2 || // Diagonal \
            countConnected(row, col, 1, -1, player) + countConnected(row, col, -1, 1, player) > 2    // Diagonal /
        );
    }

    function countConnected(row, col, dr, dc, player) {
        let count = 0;
        let r = row + dr;
        let c = col + dc;
        while (r >= 0 && r < ROWS && c >= 0 && c < COLS && board[r][c] === player) {
            count++;
            r += dr;
            c += dc;
        }
        return count;
    }
</script>

<main>
    <h1>Connect Four</h1>

    <div class="toggle">
        <button on:click={toggleMode}>
            Mode: {isVsAI ? 'Vs AI 🤖' : 'Vs Human 👥'}
        </button>
    </div>

    <div class="board">
        {#each Array(COLS) as _, colIndex (colIndex)}
            <div class="col" on:click={() => handleClick(colIndex)}>
                {#each Array(ROWS) as _, rowIndex (rowIndex)}
                    <div class="cell">
                        {#if board[rowIndex][colIndex]}
                            {board[rowIndex][colIndex]}
                        {/if}
                    </div>
                {/each}
            </div>
        {/each}
    </div>

    <p class="status">
        {#if winner}
            🎉 {winner} Wins!
        {:else if gameOver}
            🤝 It's a Draw!
        {:else}
            Turn: {currentPlayer}
        {/if}
    </p>

    <button class="restart" on:click={resetGame}>🔄 Restart Game</button>
</main>

<style>
    main {
        display: flex;
        flex-direction: column;
        align-items: center;
        padding: 1rem;
        background: #121212;
        color: #f3f3f3;
        min-height: 100vh;
    }

    h1 {
        color: #facc15;
        margin-bottom: 1rem;
        font-size: 2.5rem;
    }

    .toggle {
        margin-bottom: 1rem;
    }

    .toggle button {
        padding: 0.5rem 1rem;
        border: none;
        background: #4f46e5;
        color: white;
        border-radius: 6px;
        cursor: pointer;
        font-size: 1rem;
    }

    .toggle button:hover {
        background: #3730a3;
    }

    .board {
        display: grid;
        grid-template-columns: repeat(7, 1fr);
        gap: 6px;
        background-color: #1f2937;
        padding: 6px;
        border-radius: 12px;
        margin-bottom: 1rem;
    }

    .col {
        display: flex;
        flex-direction: column-reverse;
        gap: 6px;
        cursor: pointer;
    }

    .cell {
        width: 60px;
        height: 60px;
        background-color: #374151;
        border-radius: 50%;
        display: flex;
        align-items: center;
        justify-content: center;
        font-size: 2rem;
        user-select: none;
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
        background: #4f46e5;
        color: white;
        border: none;
        border-radius: 8px;
        cursor: pointer;
    }

    .restart:hover {
        background: #3730a3;
    }

    @media (max-width: 600px) {
        .cell {
            width: 40px;
            height: 40px;
            font-size: 1.5rem;
        }

        .board {
            gap: 4px;
            padding: 4px;
        }

        .restart, .toggle button {
            font-size: 0.9rem;
            padding: 0.5rem 1rem;
        }

        h1 {
            font-size: 1.8rem;
        }

        .status {
            font-size: 1rem;
        }
    }
</style>
