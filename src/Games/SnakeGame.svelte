<script>
	import { onMount, onDestroy } from 'svelte';

	let canvas;
	let ctx;
	let snake = [{ x: 10, y: 10 }];
	let food = { x: 15, y: 15, emoji: '🍎' };
	let direction = { x: 1, y: 0 };
	let gameOver = false;
	let score = 0;

	const gridSize = 20;
	const canvasSize = 360;
	const speed = 200;
	const fruitEmojis = ['🍎', '🍇', '🍓', '🍒'];
	let interval;

	onMount(() => {
		ctx = canvas.getContext('2d');
		canvas.focus();
		window.addEventListener('keydown', handleKeydown, { passive: false });
		initGame();
	});

	onDestroy(() => {
		window.removeEventListener('keydown', handleKeydown);
		clearInterval(interval);
	});

	function initGame() {
		gameOver = false;
		score = 0;
		snake = [{ x: 10, y: 10 }];
		direction = { x: 1, y: 0 };
		placeFood();
		draw();
		clearInterval(interval);
		interval = setInterval(moveSnake, speed);
	}

	function draw() {
		if (ctx) {
			ctx.clearRect(0, 0, canvasSize, canvasSize);
			drawSnake();
			drawFood();
			if (!gameOver) requestAnimationFrame(draw);
		}
	}

	function drawSnake() {
		ctx.shadowColor = 'rgba(0, 0, 0, 0.5)';
		ctx.shadowBlur = 4;
		snake.forEach((segment, index) => {
			ctx.beginPath();
			ctx.ellipse(
				segment.x * gridSize + gridSize / 2,
				segment.y * gridSize + gridSize / 2,
				gridSize / 2,
				gridSize / 2,
				0,
				0,
				Math.PI * 2
			);
			ctx.fillStyle = index === 0 ? '#10b981' : '#4ade80';
			ctx.fill();
			ctx.closePath();
		});
	}

	function drawFood() {
		ctx.font = '20px Arial';
		ctx.fillText(
			food.emoji,
			food.x * gridSize + gridSize / 2 - 10,
			food.y * gridSize + gridSize / 2 + 5
		);
	}

	function moveSnake() {
		if (gameOver) return;
		const head = { x: snake[0].x + direction.x, y: snake[0].y + direction.y };
		snake.unshift(head);

		if (head.x === food.x && head.y === food.y) {
			score++;
			placeFood();
		} else {
			snake.pop();
		}

		checkCollision();
	}

	function placeFood() {
		food = {
			x: Math.floor(Math.random() * (canvasSize / gridSize)),
			y: Math.floor(Math.random() * (canvasSize / gridSize)),
			emoji: fruitEmojis[Math.floor(Math.random() * fruitEmojis.length)]
		};
	}

	function checkCollision() {
		const head = snake[0];
		if (
			head.x < 0 ||
			head.x >= canvasSize / gridSize ||
			head.y < 0 ||
			head.y >= canvasSize / gridSize ||
			snake.slice(1).some(segment => segment.x === head.x && segment.y === head.y)
		) {
			gameOver = true;
		}
	}

	function handleKeydown(event) {
		if (['ArrowUp', 'ArrowDown', 'ArrowLeft', 'ArrowRight'].includes(event.key)) {
			event.preventDefault();
		}

		switch (event.key) {
			case 'ArrowUp':
				if (direction.y !== 1) direction = { x: 0, y: -1 };
				break;
			case 'ArrowDown':
				if (direction.y !== -1) direction = { x: 0, y: 1 };
				break;
			case 'ArrowLeft':
				if (direction.x !== 1) direction = { x: -1, y: 0 };
				break;
			case 'ArrowRight':
				if (direction.x !== -1) direction = { x: 1, y: 0 };
				break;
		}
	}
</script>

<main>
	<h2 class="game-heading">🐍 Snake Game</h2>

	<div class="game-wrapper">
		<canvas bind:this={canvas} width={canvasSize} height={canvasSize} tabindex="0"></canvas>
		<p class="score">Score: <span>{score}</span></p>

		{#if gameOver}
			<p class="game-over">💀 Game Over!</p>
			<button class="restart-btn" on:click={initGame}>🔁 Restart Game</button>
		{/if}
	</div>
</main>

<style>
	main {
		display: flex;
		flex-direction: column;
		align-items: center;
		min-height: 100vh;
		background-color: #111;
		box-sizing: border-box;
	}

	.game-heading {
		color: #ffffff;
		font-size: 1.6rem;
		margin-bottom: 1rem;
	}

	.game-wrapper {
		text-align: center;
		background: #1e1e1e;
		padding: 1rem;
		border-radius: 0.75rem;
		box-shadow: 0 4px 12px rgba(0, 0, 0, 0.5);
		max-width: 420px;
		width: 100%;
	}

	canvas {
		border: 2px solid #4f46e5;
		border-radius: 12px;
		background-color: #2a2a2a;
		display: block;
		margin: 0 auto 0.75rem;
		outline: none;
		width: 100%;
		max-width: 360px;
	}

	.score {
		font-size: 1.1rem;
		color: #e5e5e5;
	}

	.score span {
		color: #22c55e;
		font-weight: bold;
	}

	.game-over {
		margin-top: 0.5rem;
		font-size: 1rem;
		color: #ef4444;
		font-weight: 600;
	}

	.restart-btn {
		margin-top: 0.5rem;
		padding: 0.5rem 1rem;
		font-size: 0.9rem;
		background-color: #4f46e5;
		color: white;
		border: none;
		border-radius: 8px;
		cursor: pointer;
		transition: background-color 0.2s;
	}

	.restart-btn:hover {
		background-color: #3730a3;
	}

	@media (max-width: 480px) {
		.game-heading {
			font-size: 1.3rem;
		}

		.score,
		.restart-btn {
			font-size: 0.95rem;
		}
	}
</style>
