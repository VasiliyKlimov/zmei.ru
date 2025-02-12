
<html>
<head>
    <title>Змейка ловит мышей</title>
    <HTA:APPLICATION APPLICATIONNAME="SnakeGame" BORDER="thin" SCROLL="no" SINGLEINSTANCE="yes" />
    <style>
        body {
            margin: 0;
            overflow: hidden;
            background: url('https://i.pinimg.com/560x/e0/9f/9e/e09f9e2954c25921c998589945415592.jpg') no-repeat center center fixed;
            background-size: cover;
            font-family: Arial, sans-serif;
            color: white;
        }
        canvas {
            display: block;
            border: 5px solid black;
            margin: 10px auto;
            background-color: rgba(0, 0, 0, 0.5); /* Полупрозрачный фон для лучшей видимости */
        }
        #scoreboard {
            position: absolute;
            top: 10px;
            left: 10px;
            background: rgba(0, 0, 0, 0.7);
            padding: 10px;
            border-radius: 5px;
            width: 200px;
            text-align: center;
        }
        #highScores {
            margin-top: 10px;
        }
        #highScoresList {
            padding: 0;
            margin: 0;
        }
        button {
            margin-top: 10px;
            padding: 5px 10px;
            font-size: 14px;
            cursor: pointer;
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
            <ol id="highScoresList"></ol>
        </div>
    </div>
    <canvas id="gameCanvas"></canvas>
    <script>
        let canvas = document.getElementById("gameCanvas");
        let ctx = canvas.getContext("2d");
        canvas.width = window.innerWidth > 600 ? 600 : window.innerWidth; // Ограничение размера холста
        canvas.height = canvas.width; // Квадратное поле

        let snake = [{ x: canvas.width / 2, y: canvas.height / 2 }];
        let food = { x: Math.random() * (canvas.width - snakeSize), y: Math.random() * (canvas.height - snakeSize) };
        let snakeSize = 20;
        let dx = snakeSize, dy = 0; // Начальное направление движения
        let score = 0;
        let bestScore = 0;
        let frameRate = 100; // Скорость обновления игры в миллисекундах
        let gameStarted = false;

        // Функция для проверки столкновения с собой
        function selfCollision(head) {
            for (let segment of snake) {
                if (head.x === segment.x && head.y === segment.y) return true;
            }
            return false;
        }

        // Обработчик клавиш
        document.addEventListener('keydown', changeDirection);

        function changeDirection(event) {
            const LEFT_KEY = 37;
            const RIGHT_KEY = 39;
            const UP_KEY = 38;
            const DOWN_KEY = 40;

            const keyPressed = event.keyCode;
            const goingUp = dy === -snakeSize;
            const goingDown = dy === snakeSize;
            const goingRight = dx === snakeSize;
            const goingLeft = dx === -snakeSize;

            if (keyPressed === LEFT_KEY && !goingRight) {
                dx = -snakeSize;
                dy = 0;
            } else if (keyPressed === RIGHT_KEY && !goingLeft) {
                dx = snakeSize;
                dy = 0;
            } else if (keyPressed === UP_KEY && !goingDown) {
                dx = 0;
                dy = -snakeSize;
            } else if (keyPressed === DOWN_KEY && !goingUp) {
                dx = 0;
                dy = snakeSize;
            }
        }

        // Запуск игры по нажатию клавиши
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

                // Движение головы змеи
                let head = { x: snake[0].x + dx, y: snake[0].y + dy };

                // Проверка столкновений со стенами или собой
                if (head.x < 0 || head.x >= canvas.width || head.y < 0 || head.y >= canvas.height || selfCollision(head)) {
                    endGame();
                    return;
                }

                // Проверка поедания еды
                if (Math.hypot(head.x - food.x, head.y - food.y) < snakeSize) {
                    score++;
                    updateScore(); // Обновляем счет
                    food = generateFoodPosition(); // Генерируем новую позицию еды
                } else {
                    snake.pop(); // Удаляем последний сегмент
                }

                // Добавляем новый сегмент в начало
                snake.unshift(head);

                // Отрисовка змеи
                ctx.fillStyle = "green";
                for (let segment of snake) {
                    ctx.fillRect(segment.x, segment.y, snakeSize, snakeSize);
                }

                // Отрисовка еды
                ctx.fillStyle = "red";
                ctx.beginPath();
                ctx.arc(food.x, food.y, snakeSize / 2, 0, Math.PI * 2);
                ctx.fill();

                requestAnimationFrame(gameLoop);
            }, frameRate);
        }

        // Функция завершения игры
        function endGame() {
            if (score > bestScore) {
                bestScore = score; // Обновляем лучший результат
                updateBestScore();
                saveHighScore(score); // Сохраняем рекорд
            }
            gameStarted = false;
            alert("Игра окончена! Ваш результат: " + score);
            restartGame();
        }

        // Генерация новой позиции еды
        function generateFoodPosition() {
            let newFood = {
                x: Math.floor(Math.random() * ((canvas.width - snakeSize) / snakeSize)) * snakeSize,
                y: Math.floor(Math.random() * ((canvas.height - snakeSize) / snakeSize)) * snakeSize
            };
            return newFood;
        }

        // Обновление текущего счета
        function updateScore() {
            document.getElementById("score").textContent = score;
        }

        // Обновление лучшего счета
        function updateBestScore() {
            document.getElementById("bestScore").textContent = bestScore;
        }

        // Перезапуск игры
        function restartGame() {
            snake = [{ x: canvas.width / 2, y: canvas.height / 2 }]; // Сбрасываем змею
            dx = snakeSize;
            dy = 0;
            score = 0;
            updateScore();
            food = generateFoodPosition(); // Генерируем новую позицию еды
            gameStarted = false; // Сбрасываем флаг начала игры
            document.addEventListener('keydown', startGame); // Возобновляем слушатель события
        }

        // Сохранение рекордов
        function saveHighScore(score) {
            let highScores = JSON.parse(localStorage.getItem("highScores")) || [];
            highScores.push(score);
            highScores.sort((a, b) => b - a); // Сортируем рекорды по убыванию
            highScores = highScores.slice(0, 5); // Оставляем только топ-5 рекордов
            localStorage.setItem("highScores", JSON.stringify(highScores));
            renderHighScores();
        }

        // Отображение рекордов
        function renderHighScores() {
            let highScores = JSON.parse(localStorage.getItem("highScores")) || [];
            let highScoresList = document.getElementById("highScoresList");
            highScoresList.innerHTML = ""; // Очищаем список

            highScores.forEach((score, index) => {
                let listItem = document.createElement("li");
                listItem.textContent = `#${index + 1}: ${score}`;
                highScoresList.appendChild(listItem);
            });
        }

        // Инициализация отображения рекордов при загрузке страницы
        renderHighScores();
    </script>
</body>
</html>
