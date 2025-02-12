
<html>
<head>
    <title>Змейка ловит мышей</title>
    <HTA:APPLICATION APPLICATIONNAME="SnakeGame" BORDER="thin" SCROLL="no" SINGLEINSTANCE="yes" />
    <style>
        body {
            margin: 0;
            overflow: hidden;
            background: url('https://source.unsplash.com/800x600/?snake') no-repeat center center;
            background-size: cover;
        }
        canvas {
            display: block;
            border: 5px solid black;
        }
        #scoreboard {
            position: absolute;
            top: 10px;
            left: 10px;
            font-size: 20px;
            font-weight: bold;
            font-family: Arial, sans-serif;
            color: black;
            background: rgba(255, 255, 255, 0.7);
            padding: 10px;
            border-radius: 5px;
            display: flex;
            flex-direction: column;
            align-items: flex-start;
        }
        #scoreboard > div {
            margin-bottom: 5px;
        }
        #restartButton {
            margin-top: 10px;
            font-size: 16px;
            padding: 5px 10px;
            cursor: pointer;
        }
        #highScoresList {
            margin-top: 20px;
            color: black;
            background: rgba(255, 255, 255, 0.7);
            padding: 10px;
            border-radius: 5px;
            max-height: 300px;
            overflow-y: auto;
        }
        #highScoresList li {
            margin-bottom: 5px;
        }
    </style>
</head>
<body>
    <div id="scoreboard">
        <div>Лучший результат: <span id="bestScore">0</span></div>
        <div>Текущий результат: <span id="score">0</span></div>
        <button id="restartButton" onclick="restartGame()">Перезапуск</button>
        <div id="highScores">
            <h3>Рекорды:</h3>
            <ol id="highScoresList">
            </ol>
        </div>
    </div>
    <canvas id="gameCanvas"></canvas>
    <script>
        let canvas = document.getElementById("gameCanvas");
        let ctx = canvas.getContext("2d");
        canvas.width = 800;
        canvas.height = 600;

        let snake = [{ x: 400, y: 300 }];
        let food = { x: Math.random() * (canvas.width - 20), y: Math.random() * (canvas.height - 20) };
        let snakeSize = 20;
        let speed = 20;
        let dx = speed;
        let dy = 0;
        let score = 0;
        let bestScore = localStorage.getItem("bestScore") || 0;
        let frameRate = 100;

        document.getElementById("bestScore").innerText = bestScore;

        let highScores = JSON.parse(localStorage.getItem("highScores")) || [];

        function updateHighScores() {
            highScores.push(score);
            highScores.sort((a, b) => b - a);
            highScores = highScores.slice(0, 500);

            localStorage.setItem("highScores", JSON.stringify(highScores));
            renderHighScores();
        }

        function renderHighScores() {
            const highScoresList = document.getElementById("highScoresList");
            highScoresList.innerHTML = "";

            highScores.forEach((score, index) => {
                const li = document.createElement("li");
                li.textContent = `${index + 1}. ${score}`;
                highScoresList.appendChild(li);
            });
        }

        renderHighScores();

        document.addEventListener("keydown", function (event) {
            switch (event.key) {
                case "ArrowUp":
                    if (dy === 0) {
                        dy = -speed;
                        dx = 0;
                    }
                    break;
                case "ArrowDown":
                    if (dy === 0) {
                        dy = speed;
                        dx = 0;
                    }
                    break;
                case "ArrowLeft":
                    if (dx === 0) {
                        dx = -speed;
                        dy = 0;
                    }
                    break;
                case "ArrowRight":
                    if (dx === 0) {
                        dx = speed;
                        dy = 0;
                    }
                    break;
            }
        });

        function gameLoop() {
            setTimeout(() => {
                ctx.clearRect(0, 0, canvas.width, canvas.height);

                let head = { x: snake[0].x + dx, y: snake[0].y + dy };

                if (head.x < 0 || head.x >= canvas.width || head.y < 0 || head.y >= canvas.height || selfCollision(head)) {
                    if (score > bestScore) {
                        bestScore = score;
                        localStorage.setItem("bestScore", bestScore);
                    }
                    alert("Игра окончена! Ваш результат: " + score);
                    restartGame();
                    return;
                }

                snake.unshift(head);

                if (Math.hypot(head.x - food.x, head.y - food.y) < snakeSize) {
                    score++;
                    document.getElementById("score").innerText = score;
                    food.x = Math.random() * (canvas.width - 20);
                    food.y = Math.random() * (canvas.height - 20);
                    if (score % 100 === 0) frameRate *= 0.9;
                    updateHighScores(); // Обновляем список рекордов
                } else {
                    snake.pop();
                }

                ctx.fillStyle = "green";
                snake.forEach(segment => {
                    ctx.fillRect(segment.x, segment.y, snakeSize, snakeSize);
                });

                ctx.fillStyle = "white";
                ctx.beginPath();
                ctx.arc(food.x, food.y, 10, 0, Math.PI * 2);
                ctx.fill();
                ctx.strokeStyle = "black";
                ctx.lineWidth = 2;
                ctx.stroke();

                requestAnimationFrame(gameLoop);
            }, frameRate);
        }

        function selfCollision(head) {
            for (let i = 1; i < snake.length; i++) {
                if (head.x === snake[i].x && head.y === snake[i].y) {
                    return true;
                }
            }
            return false;
        }

        function restartGame() {
            score = 0;
            document.getElementById("score").innerText = score;
            snake = [{ x: 400, y: 300 }];
            dx = speed;
            dy = 0;
            frameRate = 100;
            gameLoop();
            renderHighScores(); // Обновляем список рекордов после перезапуска
        }

        gameLoop();
    </script>
</body>
</html>
