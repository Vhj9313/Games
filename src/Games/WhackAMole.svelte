<script>
  import { onMount, onDestroy } from 'svelte';
  import { writable } from 'svelte/store';

  const holes = 9;
  let molePosition = writable(-1);
  let score = writable(0);
  let timeLeft = writable(30);
  let gameInterval;
  let timerInterval;

  function startGame() {
    score.set(0);
    timeLeft.set(30);
    nextMole();

    timerInterval = setInterval(() => {
      timeLeft.update(n => {
        if (n <= 1) {
          stopGame();
          return 0;
        }
        return n - 1;
      });
    }, 1000);
  }

  function nextMole() {
    gameInterval = setInterval(() => {
      molePosition.set(Math.floor(Math.random() * holes));
    }, 800);
  }

  function whack(index) {
    molePosition.update(pos => {
      if (pos === index) {
        score.update(s => s + 1);
        return -1;
      }
      return pos;
    });
  }

  function stopGame() {
    clearInterval(gameInterval);
    clearInterval(timerInterval);
    molePosition.set(-1);
  }

  function restart() {
    stopGame();
    startGame();
  }

  onMount(() => startGame());
  onDestroy(() => stopGame());
</script>

<main>
  <h1 class="title">🐹 Whack A Mole</h1>
  <p class="score">Score: {$score}</p>
  <p class="timer">Time left: {$timeLeft}s</p>

  <div class="grid">
    {#each Array(holes) as _, index}
      <div
        class="hole"
        on:click={() => whack(index)}
      >
        {#if $molePosition === index}
          <span class="mole">🐹</span>
        {/if}
      </div>
    {/each}
  </div>

  <button class="restart" on:click={restart}>🔄 Restart</button>
</main>

<style>
  main {
    text-align: center;
    padding: 1rem;
    background-color: #121212;
    color: #e5e5e5;
    min-height: 100vh;
  }

  .title {
    font-size: 2rem;
    margin-bottom: 1rem;
    color: #facc15;
  }

  .score, .timer {
    font-size: 1.2rem;
    margin-bottom: 0.5rem;
    color: #a3e635;
  }

  .grid {
    display: grid;
    grid-template-columns: repeat(3, 80px);
    grid-gap: 10px;
    justify-content: center;
    margin: 1rem auto;
  }

  .hole {
    width: 80px;
    height: 80px;
    background-color: #333;
    border-radius: 50%;
    position: relative;
    cursor: pointer;
    overflow: hidden;
    display: flex;
    align-items: center;
    justify-content: center;
    transition: background 0.3s;
  }

  .hole:hover {
    background-color: #444;
  }

  .mole {
    font-size: 2rem;
    animation: pop 0.3s ease;
  }

  @keyframes pop {
    0% { transform: translateY(100%); opacity: 0; }
    100% { transform: translateY(0); opacity: 1; }
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
  }

  .restart:hover {
    background-color: #3730a3;
  }

  @media (max-width: 500px) {
    .grid {
      grid-template-columns: repeat(3, 60px);
      grid-gap: 8px;
    }

    .hole {
      width: 60px;
      height: 60px;
    }

    .mole {
      font-size: 1.5rem;
    }

    .title {
      font-size: 1.5rem;
    }

    .score, .timer {
      font-size: 1rem;
    }
  }
</style>
