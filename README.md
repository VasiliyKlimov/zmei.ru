
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
            font-family: sans-serif; /* Добавим шрифт для читаемости текста */
            color: white; /* Белый текст для лучшей видимости на фоне */
        }
        canvas {
            display: block;
            border: 5px solid black;
            background-color: rgba(0, 0, 0, 0.7); /* Полупрозрачный черный фон для canvas, чтобы змейка была лучше видна на фоне */
        }
        #scoreboard {
            position: absolute;
            top: 10px;
            left: 10px;
            padding: 15px;
            background-color: rgba(0, 0, 0, 0.6); /* Полупрозрачный фон для scoreboard */
            border-radius: 5px;
        }
        #scoreboard div {
            margin-bottom: 8px;
        }
        #highScores {
            margin-top: 15px;
        }
        #highScoresList {
            padding-left: 20px;
            margin-top: 5px;
        }
        #restartButton {
            padding: 8px 15px;
            background-color: #4CAF50; /* Зеленый цвет кнопки */
            color: white;
            border: none;
            border-radius: 5px;
            cursor: pointer;
            font-size: 16px;
        }
        #restartButton:hover {
            background-color: #45a049; /* Более темный зеленый при наведении */
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
        const canvas = document.getElementById('gameCanvas');
        const ctx = canvas.getContext('2d');
        const scoreDisplay = document.getElementById('score');
        const bestScoreDisplay = document.getElementById('bestScore');
        const highScoresList = document.getElementById('highScoresList');

        const gridSize = 20; // Размер клетки сетки
        let snakeSize = gridSize; // Размер змейки равен размеру клетки
        let canvasSize = 400; // Размер канваса
        canvas.width = canvasSize;
        canvas.height = canvasSize;

        let snake = [{ x: 160, y: 160 }]; // Начальная позиция змейки
        let food = {};
        let dx = gridSize; // Начальное направление по X (вправо)
        let dy = 0;        // Начальное направление по Y (не двигается)
        let score = 0;
        let bestScore = localStorage.getItem('bestScore') || 0; // Получаем лучший результат из localStorage
        bestScoreDisplay.textContent = bestScore;
        let highScores = JSON.parse(localStorage.getItem('highScores') || '[]'); // Получаем рекорды из localStorage
        updateHighScoresList();

        let frameRate = 150; // Скорость игры (миллисекунды между кадрами)
        let gameStarted = false;
        let gameLoopInterval;

        // Изображение мыши
        const foodImage = new Image();
        foodImage.src = 'https://i.imgur.com/yAoEa5M.png'; // Замените на URL изображения мыши

        // Функция для генерации случайной позиции для еды
        function generateFood() {
            let newFoodPosition;
            do {
                newFoodPosition = {
                    x: Math.floor(Math.random() * (canvas.width / gridSize)) * gridSize,
                    y: Math.floor(Math.random() * (canvas.height / gridSize)) * gridSize
                };
            } while (snake.some(segment => segment.x === newFoodPosition.x && segment.y === newFoodPosition.y)); // Проверяем, не на змейке ли еда
            food = newFoodPosition;
        }

        // Функция для проверки столкновения змейки с собой
        function selfCollision(head) {
            for (let i = 1; i < snake.length; i++) {
                if (head.x === snake[i].x && head.y === snake[i].y) {
                    return true;
                }
            }
            return false;
        }

        // Функция для отрисовки змейки
        function drawSnake() {
            snake.forEach((segment, index) => {
                ctx.fillStyle = index === 0 ? 'lightgreen' : 'green'; // Голова змейки светлее
                ctx.fillRect(segment.x, segment.y, snakeSize, snakeSize);
                ctx.strokeStyle = 'darkgreen'; // Обводка для сегментов
                ctx.strokeRect(segment.x, segment.y, snakeSize, snakeSize);
            });
        }

        // Функция для отрисовки еды (мыши)
        function drawFood() {
            if (foodImage.complete) { // Убедимся, что изображение загружено
                ctx.drawImage(foodImage, food.x, food.y, snakeSize, snakeSize);
            } else {
                foodImage.onload = () => { // Если нет, загрузим и нарисуем после загрузки
                    ctx.drawImage(foodImage, food.x, food.y, snakeSize, snakeSize);
                };
            }
        }


        // Основной игровой цикл
        function gameLoop() {
            if (!gameStarted) return;

            gameLoopInterval = setTimeout(() => {
                ctx.clearRect(0, 0, canvas.width, canvas.height);

                let head = { x: snake[0].x + dx, y: snake[0].y + dy };

                // Проверка столкновений со стенами и с собой
                if (head.x < 0 || head.x >= canvas.width || head.y < 0 || head.y >= canvas.height || selfCollision(head)) {
                    gameOver();
                    return; // Выход из gameLoop после завершения игры
                }

                snake.unshift(head); // Добавляем новую голову в начало змейки

                // Проверка, съела ли змейка еду
                if (head.x === food.x && head.y === food.y) {
                    score++;
                    scoreDisplay.textContent = score;
                    generateFood(); // Генерируем новую еду
                    if (score > bestScore) {
                        bestScore = score;
                        bestScoreDisplay.textContent = bestScore;
                        localStorage.setItem('bestScore', bestScore); // Сохраняем лучший результат
                    }
                } else {
                    snake.pop(); // Убираем хвост, если еда не была съедена
                }

                drawFood();
                drawSnake();
                requestAnimationFrame(gameLoop); // Запускаем следующий кадр анимации
            }, frameRate);
        }

        function gameOver() {
            gameStarted = false;
            clearInterval(gameLoopInterval); // Очищаем setTimeout
            alert(`Игра окончена! Ваш результат: ${score}`);

            // Обновление рекордов
            highScores.push({ score: score, date: new Date().toLocaleDateString() });
            highScores.sort((a, b) => b.score - a.score); // Сортировка по убыванию
            highScores = highScores.slice(0, 5); // Оставляем только топ 5
            localStorage.setItem('highScores', JSON.stringify(highScores)); // Сохраняем рекорды
            updateHighScoresList();

            restartGame(); // Подготавливаем игру к перезапуску
        }

        function updateHighScoresList() {
            highScoresList.innerHTML = ''; // Очищаем список
            highScores.forEach(record => {
                const li = document.createElement('li');
                li.textContent = `${record.score} - ${record.date}`;
                highScoresList.appendChild(li);
            });
        }


        function restartGame() {
            gameStarted = false;
            clearInterval(gameLoopInterval); // Убедимся, что предыдущий цикл остановлен
            snake = [{ x: 160, y: 160 }]; // Возвращаем змейку в начальное положение
            dx = gridSize;
            dy = 0; // Сбрасываем направление
            score = 0;
            scoreDisplay.textContent = score;
            generateFood(); // Генерируем еду заново
            document.addEventListener('keydown', startGame); // Возвращаем слушатель для начала игры
            document.getElementById('restartButton').textContent = 'Перезапуск'; // Обновляем текст кнопки
        }

        function startGame() {
            if (!gameStarted) {
                gameStarted = true;
                document.removeEventListener('keydown', startGame); // Убираем слушатель события старта
                generateFood(); // Генерируем еду при старте игры
                gameLoop();
                document.getElementById('restartButton').textContent = 'Перезапустить'; // Меняем текст кнопки после старта
            }
        }

        // Обработка нажатий клавиш для управления змейкой
        document.addEventListener('keydown', startGame); // Запуск игры по нажатию любой клавиши (первый старт)
        document.addEventListener('keydown', changeDirection);

        function changeDirection(event) {
            if (!gameStarted) return; // Игнорируем ввод, если игра не запущена

            const keyPressed = event.keyCode;
            const LEFT = 37;
            const UP = 38;
            const RIGHT = 39;
            const DOWN = 40;

            const goingUp = dy === -gridSize;
            const goingDown = dy === gridSize;
            const goingLeft = dx === -gridSize;
            const goingRight = dx === gridSize;

            if (keyPressed === LEFT && !goingRight) {
                dx = -gridSize;
                dy = 0;
            }
            if (keyPressed === UP && !goingDown) {
                dx = 0;
                dy = -gridSize;
            }
            if (keyPressed === RIGHT && !goingLeft) {
                dx = gridSize;
                dy = 0;
            }
            if (keyPressed === DOWN && !goingUp) {
                dx = 0;
                dy = gridSize;
            }
        }

        // Генерация еды при загрузке страницы (чтобы еда была видна сразу после загрузки)
        generateFood();
        drawFood(); // Отображаем еду до начала игры
        drawSnake(); // Отображаем змейку в начальной позиции до начала игры
    </script>
</body>
</html>
