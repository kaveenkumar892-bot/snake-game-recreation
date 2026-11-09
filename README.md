# snake-game-recreation
It's just a snake game


<!DOCTYPE html>
<html>
<head>
<title>Retro Snake Game</title>
<style>
body {
    background: #101820;
    color: #00ff66;
    font-family: monospace;
    text-align: center;
}

h1 {
    font-size: 35px;
    text-shadow: 0 0 10px #00ff66;
}

canvas {
    background: black;
    border: 4px solid #00ff66;
    box-shadow: 0 0 15px #00ff66;
}

button {
    background: #00ff66;
    color: black;
    border: none;
    padding: 12px 25px;
    margin: 10px;
    font-weight: bold;
    cursor: pointer;
    font-family: monospace;
}

button:hover {
    background: white;
}

#score {
    font-size: 22px;
}
</style>
</head>

<body>

<h1>🐍 RETRO SNAKE</h1>
<p id="score">Score: 0</p>

<canvas id="game" width="400" height="400"></canvas>

<br>
<button onclick="startGame()">START</button>
<button onclick="pauseGame()">PAUSE</button>
<button onclick="startGame()">RESTART</button>

<script>
const canvas = document.getElementById("game");
const ctx = canvas.getContext("2d");

const box = 20;
let snake, food, direction, score, game, speed;
let running = false;
let paused = false;

function startGame() {
    clearInterval(game);

    snake = [{x: 200, y: 200}];
    food = {
        x: Math.floor(Math.random() * 20) * box,
        y: Math.floor(Math.random() * 20) * box
    };

    direction = "RIGHT";
    score = 0;
    speed = 130;
    running = true;
    paused = false;

    document.getElementById("score").innerText =
        "Score: " + score;

    game = setInterval(draw, speed);
}

function pauseGame() {
    if (!running) return;

    if (paused) {
        paused = false;
        game = setInterval(draw, speed);
    } else {
        paused = true;
        clearInterval(game);
        ctx.fillStyle = "white";
        ctx.font = "25px monospace";
        ctx.fillText("PAUSED", 140, 210);
    }
}

document.addEventListener("keydown", function(e) {
    const keys = {
        ArrowUp: "UP",
        ArrowDown: "DOWN",
        ArrowLeft: "LEFT",
        ArrowRight: "RIGHT"
    };

    let newDirection = keys[e.key];

    if (!newDirection) return;
    e.preventDefault();

    if (newDirection === "UP" && direction !== "DOWN")
        direction = "UP";
    if (newDirection === "DOWN" && direction !== "UP")
        direction = "DOWN";
    if (newDirection === "LEFT" && direction !== "RIGHT")
        direction = "LEFT";
    if (newDirection === "RIGHT" && direction !== "LEFT")
        direction = "RIGHT";
});

function draw() {
    if (!running || paused) return;

    ctx.fillStyle = "black";
    ctx.fillRect(0, 0, 400, 400);

    // Draw food
    ctx.fillStyle = "red";
    ctx.fillRect(food.x, food.y, box, box);

    // Draw snake
    snake.forEach((part, index) => {
        ctx.fillStyle = index === 0 ? "#00ff66" : "#00aa44";
        ctx.fillRect(part.x, part.y, box - 2, box - 2);
    });

    let head = {...snake[0]};

    if (direction === "UP") head.y -= box;
    if (direction === "DOWN") head.y += box;
    if (direction === "LEFT") head.x -= box;
    if (direction === "RIGHT") head.x += box;

    // Collision detection
    if (
        head.x < 0 || head.x >= 400 ||
        head.y < 0 || head.y >= 400 ||
        snake.some(part => part.x === head.x && part.y === head.y)
    ) {
        endGame();
        return;
    }

    snake.unshift(head);

    // Eat food
    if (head.x === food.x && head.y === food.y) {
        score += 10;

        document.getElementById("score").innerText =
            "Score: " + score;

        food = {
            x: Math.floor(Math.random() * 20) * box,
            y: Math.floor(Math.random() * 20) * box
        };

        clearInterval(game);
        speed = Math.max(55, speed - 3);
        game = setInterval(draw, speed);

    } else {
        snake.pop();
    }
}

function endGame() {
    clearInterval(game);
    running = false;

    ctx.fillStyle = "red";
    ctx.font = "30px monospace";
    ctx.fillText("GAME OVER", 105, 190);

    ctx.fillStyle = "white";
    ctx.font = "16px monospace";
    ctx.fillText("Press RESTART to play again", 75, 225);
}
</script>

</body>
</html>
