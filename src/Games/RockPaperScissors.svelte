<script>
    let choices = ['✊ Rock', '✋ Paper', '✌️ Scissors'];
    let playerChoice = '';
    let computerChoice = '';
    let result = '';

    function play(player) {
        playerChoice = choices[player];
        const computer = Math.floor(Math.random() * 3);
        computerChoice = choices[computer];

        if (player === computer) {
            result = "It's a Tie!";
        } else if (
            (player === 0 && computer === 2) ||
            (player === 1 && computer === 0) ||
            (player === 2 && computer === 1)
        ) {
            result = "🎉 You Win!";
        } else {
            result = "💻 Computer Wins!";
        }
    }

    function resetGame() {
        playerChoice = '';
        computerChoice = '';
        result = '';
    }
</script>

<main>
    <h2 class="heading">✊ Rock ✋ Paper ✌️ Scissors</h2>

    <div class="buttons">
        {#each choices as choice, index}
            <button on:click={() => play(index)}>{choice}</button>
        {/each}
    </div>

    {#if result}
        <div class="result">
            <p><strong>You:</strong> {playerChoice}</p>
            <p><strong>Computer:</strong> {computerChoice}</p>
            <h3>{result}</h3>
            <button class="restart" on:click={resetGame}>🔄 Play Again</button>
        </div>
    {/if}
</main>

<style>
    main {
        text-align: center;
        padding: 1rem;
        font-family: 'Segoe UI', sans-serif;
        background-color: #121212;
        color: #f3f3f3;
        min-height: 100vh;
    }

    .heading {
        font-size: 2rem;
        margin-bottom: 1rem;
        color: #facc15;
    }

    .buttons {
        display: flex;
        justify-content: center;
        gap: 1rem;
        flex-wrap: wrap;
        margin-bottom: 1.5rem;
    }

    button {
        font-size: 1.2rem;
        padding: 0.8rem 1.2rem;
        background-color: #4f46e5;
        color: white;
        border: none;
        border-radius: 8px;
        cursor: pointer;
        transition: background-color 0.3s;
    }

    button:hover {
        background-color: #3730a3;
    }

    .result {
        margin-top: 1rem;
        font-size: 1.1rem;
    }

    .result h3 {
        margin-top: 0.5rem;
        color: #a3e635;
    }

    .restart {
        margin-top: 1rem;
        background-color: #10b981;
    }

    .restart:hover {
        background-color: #047857;
    }

    @media (max-width: 600px) {
        .heading {
            font-size: 1.5rem;
        }

        button {
            font-size: 1rem;
            padding: 0.6rem 1rem;
        }

        .result {
            font-size: 1rem;
        }
    }
</style>
