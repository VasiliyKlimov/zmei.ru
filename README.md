<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Snake Game</title>
    <style>
        body {
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            height: 100vh;
            background-color: black;
            margin: 0;
            color: white;
            font-family: Arial, sans-serif;
        }
        canvas {
            background-color: #111;
        }
        #score {
            font-size: 20px;
            margin-bottom: 10px;
        }
        #restartBtn {
            display: none;
            margin-top: 10px;
            padding: 10px 20px;
            font-size: 16px;
            background-color: red;
            color: white;
            border: none;
            cursor: pointer;
        }
        #highScores {
            margin-top: 10px;
        }
    </style>
</head>
<body>
    <div id="score">Score: 0</div>
    <canvas id="gameCanvas" width="900" height="900"></canvas>
    <button id="restartBtn" onclick="restartGame()">Restart</button>
    <div id="highScores">
        <h3>High Scores</h3>
        <ul id="scoreList"></ul>
    </div>
    <script>
        const canvas = document.getElementById("gameCanvas");
        const ctx = canvas.getContext("2d");
        const scoreDisplay = document.getElementById("score");
        const restartBtn = document.getElementById("restartBtn");
        const scoreList = document.getElementById("scoreList");

        const gridSize = 20;
        let snake, direction, food, gameOver, score, speed, gameInterval;
        let highScores = JSON.parse(localStorage.getItem("highScores")) || [];

        function init() {
            snake = [{ x: 200, y: 200 }];
            direction = { x: 0, y: 0 };
            food = { x: 100, y: 100 };
            gameOver = false;
            score = 0;
            speed = 200;
            scoreDisplay.innerText = "Score: 0";
            restartBtn.style.display = "none";
            clearInterval(gameInterval);
            gameInterval = setInterval(gameLoop, speed);
        }

        function drawRect(x, y, color) {
            ctx.fillStyle = color;
            ctx.fillRect(x, y, gridSize, gridSize);
        }

        function checkCollision() {
            let head = snake[0];
            if (head.x < 0 || head.x >= canvas.width || head.y < 0 || head.y >= canvas.height) {
                gameOver = true;
            }
            for (let i = 1; i < snake.length; i++) {
                if (head.x === snake[i].x && head.y === snake[i].y) {
                    gameOver = true;
                }
            }
        }

        function update() {
            if (gameOver) return;
            if (direction.x === 0 && direction.y === 0) return;
            let head = { x: snake[0].x + direction.x * gridSize, y: snake[0].y + direction.y * gridSize };
            snake.unshift(head);
            checkCollision();
            if (gameOver) {
                saveScore();
                return;
            }
            if (head.x === food.x && head.y === food.y) {
                score += 10;
                scoreDisplay.innerText = "Score: " + score;
                food = {
                    x: Math.floor(Math.random() * (canvas.width / gridSize)) * gridSize,
                    y: Math.floor(Math.random() * (canvas.height / gridSize)) * gridSize
                };
                if (score % 50 === 0) {
                    speed = Math.max(50, speed - 20);
                    clearInterval(gameInterval);
                    gameInterval = setInterval(gameLoop, speed);
                }
            } else {
                snake.pop();
            }
        }

        function draw() {
            ctx.clearRect(0, 0, canvas.width, canvas.height);
            if (gameOver) {
                ctx.fillStyle = "red";
                ctx.font = "30px Arial";
                ctx.fillText("GAME OVER", canvas.width / 4, canvas.height / 2);
                restartBtn.style.display = "block";
                return;
            }
            snake.forEach((segment, index) => {
                drawRect(segment.x, segment.y, index === 0 ? "red" : "green");
            });
            drawRect(food.x, food.y, "white");
        }

        function gameLoop() {
            update();
            draw();
        }

        function saveScore() {
            highScores.push(score);
            highScores.sort((a, b) => b - a);
            highScores = highScores.slice(0, 5);
            localStorage.setItem("highScores", JSON.stringify(highScores));
            updateHighScores();
        }

        function updateHighScores() {
            scoreList.innerHTML = "";
            highScores.forEach((s, index) => {
                let li = document.createElement("li");
                li.textContent = `${index + 1}. ${s}`;
                scoreList.appendChild(li);
            });
        }

        function restartGame() {
            init();
        }

        init();
        updateHighScores();
        document.addEventListener("keydown", (event) => {
            switch (event.key) {
                case "ArrowUp": if (direction.y === 0) direction = { x: 0, y: -1 }; break;
                case "ArrowDown": if (direction.y === 0) direction = { x: 0, y: 1 }; break;
                case "ArrowLeft": if (direction.x === 0) direction = { x: -1, y: 0 }; break;
                case "ArrowRight": if (direction.x === 0) direction = { x: 1, y: 0 }; break;
            }
        });
    </script>
</body>
</html>
