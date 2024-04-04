<script>
  import { onMount } from 'svelte';

  let canvas;
  let ctx;
  let tileSize = 10;
  let rows;
  let cols;
  let snake;
  let food;
  let score = 0;
  let dx = tileSize;
  let dy = 0;
  let isPaused = false;

  function resetGame() {
    snake = [{ x: 10, y: 10 }];
    food = { x: Math.floor(Math.random() * cols), y: Math.floor(Math.random() * rows) };
    score = 0;
    dx = tileSize;
    dy = 0;
    isPaused = false;
  }

  function draw() {
    ctx.clearRect(0, 0, canvas.width, canvas.height);
    ctx.fillStyle = 'green';
    snake.forEach(segment => {
      ctx.fillRect(segment.x * tileSize, segment.y * tileSize, tileSize, tileSize);
    });

    ctx.fillStyle = 'red';
    ctx.fillRect(food.x * tileSize, food.y * tileSize, tileSize, tileSize);
  }

  function moveSnake() {
    if (!isPaused) {
      const head = { x: snake[0].x + dx / tileSize, y: snake[0].y + dy / tileSize };
      snake.unshift(head);
      if (head.x === food.x && head.y === food.y) {
        score++;
        food = { x: Math.floor(Math.random() * cols), y: Math.floor(Math.random() * rows) };
      } else {
        snake.pop();
      }
    }
  }

  function checkCollision() {
    if (
      snake[0].x < 0 ||
      snake[0].x >= cols ||
      snake[0].y < 0 ||
      snake[0].y >= rows ||
      snake.slice(1).some(segment => segment.x === snake[0].x && segment.y === snake[0].y)
    ) {
      drawBlast();
      resetGame();
    }
  }

  function drawBlast() {
    ctx.fillStyle = 'black';
    ctx.font = '48px serif';
    ctx.fillText('💥', canvas.width / 2 - 20, canvas.height / 2 + 20);
  }

  function update() {
    moveSnake();
    checkCollision();
    draw();
  }

  onMount(() => {
    canvas = document.getElementById('canvas');
    ctx = canvas.getContext('2d');
    cols = Math.floor(canvas.width / tileSize);
    rows = Math.floor(canvas.height / tileSize);
    resetGame();
    setInterval(update, 100);
  });

  function handleKeyDown(event) {
    const keyPressed = event.key;
    if (keyPressed === 'ArrowLeft' && dx === 0) {
      dx = -tileSize;
      dy = 0;
    } else if (keyPressed === 'ArrowRight' && dx === 0) {
      dx = tileSize;
      dy = 0;
    } else if (keyPressed === 'ArrowUp' && dy === 0) {
      dx = 0;
      dy = -tileSize;
    } else if (keyPressed === 'ArrowDown' && dy === 0) {
      dx = 0;
      dy = tileSize;
    } else if (keyPressed === ' ') { 
      isPaused = !isPaused;
    }
  }
</script>

<canvas id="canvas" width="400" height="400" style="border:1px solid black;" on:keydown={handleKeyDown} tabindex="0"></canvas>

<p>Score: {score}</p>
<p>Press Space to Pause/Play</p>
