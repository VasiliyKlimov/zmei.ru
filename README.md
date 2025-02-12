
<html>
<head>
    <title>Змейка ловит мышей</title>
    <HTA:APPLICATION APPLICATIONNAME="SnakeGame" BORDER="thin" SCROLL="no" SINGLEINSTANCE="yes" />
    <style>
        body {
            margin: 0;
            overflow: hidden;
            /* Используем несколько изображений змей для фона */
            background: url('https://i.pinimg.com/560x/e0/9f/9e/e09f9e2954c25921c998589945415592.jpg') no-repeat center center;
            background-size: cover;
        }
        canvas {
            display: block;
            border: 5px solid black;
        }
        /* ... (остальные стили без изменений) */
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
        // ... (переменные и функции без изменений)

        let gameStarted = false; // Флаг, indicating whether the game has started

        // Запускаем игру по нажатию любой клавиши
        document.addEventListener('keydown', startGame);

        function startGame() {
            if (!gameStarted) {
                gameStarted = true;
                document.removeEventListener('keydown', startGame); // Убираем слушатель события
                gameLoop();
            }
        }


        function gameLoop() {
            if (!gameStarted) return; // Проверяем, запущена ли игра

            setTimeout(() => {
                ctx.clearRect(0, 0, canvas.width, canvas.height);

                let head = { x: snake[0].x + dx, y: snake[0].y + dy };

                if (head.x < 0 || head.x >= canvas.width || head.y < 0 || head.y >= canvas.height || selfCollision(head)) {
                    // ... (логика завершения игры)
                }

                snake.unshift(head);

                if (Math.hypot(head.x - food.x, head.y - food.y) < snakeSize) {
                    // ... (логика съедания еды)
                } else {
                    snake.pop();
                }

                // ... (отрисовка)
                requestAnimationFrame(gameLoop);
            }, frameRate);
        }

        function restartGame() {
            gameStarted = false; // Сбрасываем флаг
            document.addEventListener('keydown', startGame); // Возобновляем слушатель события
            // ... (остальная логика перезапуска)
        }

        // ... (остальной код)
    </script>
</body>
</html>
