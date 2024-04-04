<script>
  import { onMount } from "svelte"

  let canvas
  let ctx
  let tileSize = 4
  let rows
  let cols
  let snake
  let food
  let score = 0
  let dx = tileSize
  let dy = 0
  let isPaused = false

  const resetGame = () => {
    snake = [{ x: 10, y: 10 }]
    food = { x: Math.floor(Math.random() * cols), y: Math.floor(Math.random() * rows) }
    score = 0
    dx = tileSize
    dy = 0
    isPaused = false
  }

  const draw = () => {
    ctx.clearRect(0, 0, canvas.width, canvas.height)

    // ctx.strokeStyle = "#166af2"
    ctx.lineWidth = 0.2
    ctx.strokeStyle = "#f0f3f7"
    // ctx.strokeStyle = "rgba(255, 255, 255, 0.1)"
    for (let i = 0; i < rows; i++) {
      ctx.beginPath()
      ctx.moveTo(0, i * tileSize)
      ctx.lineTo(canvas.width, i * tileSize)
      ctx.stroke()
    }
    for (let i = 0; i < cols; i++) {
      ctx.beginPath()
      ctx.moveTo(i * tileSize, 0)
      ctx.lineTo(i * tileSize, canvas.height)
      ctx.stroke()
    }

    ctx.fillStyle = "green"
    snake.forEach((segment) => {
      ctx.fillRect(segment.x * tileSize, segment.y * tileSize, tileSize, tileSize)
    })

    ctx.fillStyle = "red"
    ctx.fillRect(food.x * tileSize, food.y * tileSize, tileSize, tileSize)
  }

  const moveSnake = () => {
    if (!isPaused) {
      const head = { x: snake[0].x + dx / tileSize, y: snake[0].y + dy / tileSize }
      snake.unshift(head)
      if (head.x === food.x && head.y === food.y) {
        score++
        food = { x: Math.floor(Math.random() * cols), y: Math.floor(Math.random() * rows) }
      } else {
        snake.pop()
      }
    }
  }

  const checkCollision = () => {
    if (snake[0].x < 0 || snake[0].x >= cols || snake[0].y < 0 || snake[0].y >= rows || snake.slice(1).some((segment) => segment.x === snake[0].x && segment.y === snake[0].y)) {
      drawBlast()
      resetGame()
    }
  }

  const drawBlast = () => {
    ctx.fillStyle = "black"
    ctx.font = "48px serif"
    ctx.fillText("💥", canvas.width / 2 - 20, canvas.height / 2 + 20)
  }

  const update = () => {
    moveSnake()
    checkCollision()
    draw()
  }

  onMount(() => {
    canvas = document.getElementById("canvas")
    ctx = canvas.getContext("2d")
    cols = Math.floor(canvas.width / tileSize)
    rows = Math.floor(canvas.height / tileSize)
    resetGame()
    isPaused = true
    setInterval(update, 100)
  })

  const handleKeyDown = (event) => {
    const keyPressed = event.key
    if (keyPressed === "ArrowLeft" && dx === 0) {
      dx = -tileSize
      dy = 0
    } else if (keyPressed === "ArrowRight" && dx === 0) {
      dx = tileSize
      dy = 0
    } else if (keyPressed === "ArrowUp" && dy === 0) {
      dx = 0
      dy = -tileSize
    } else if (keyPressed === "ArrowDown" && dy === 0) {
      dx = 0
      dy = tileSize
    }
    // else if (keyPressed === " ") {
    //   isPaused = !isPaused
    // }
  }
  const playControl = (e) => {
    isPaused = !isPaused
  }
</script>

<svelte:window on:keydown={handleKeyDown} />
<div class="relative flex h-screen w-screen flex-col items-center justify-center overflow-hidden bg-gradient-to-r from-teal-400 to-gray-800">
  <div class="absolute top-3 right-3 text-2xl font-semibold text-white">Score: {score}</div>
  <canvas id="canvas" class="h-[60%] w-[60%] rounded-md border border-dashed border-gray-900 bg-gray-500" />
  <!-- <div>Press Space to Pause/Play</div> -->
  <div class="flex w-full items-center justify-center">
    <button on:click={playControl} type="button" class="{isPaused ? 'bg-green-500' : 'bg-red-500'} w-32 rounded-md mt-5 px-5 py-2 text-white">{isPaused ? "Start" : "Stop"}</button>
  </div>
</div>
