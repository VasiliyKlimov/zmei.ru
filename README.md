
<html>
<head>
    <title>Мухобойка 8-bit</title>
    <style>
        body {
            background: url('https://i.imgur.com/3ZQZQ9m.png') repeat;
            image-rendering: pixelated;
            text-align: center;
            font-family: 'Press Start 2P', cursive;
            color: white;
            overflow: hidden; /* Предотвращаем прокрутку */
            margin: 0; /* Убираем отступы body */
        }
        h1, p {
            margin: 10px 0; /* Уменьшаем margin для заголовка и параграфа */
        }
        canvas {
            display: block;
            margin: 20px auto;
            border: 3px solid white;
            background: #73c48f;
            image-rendering: pixelated;
            cursor: crosshair; /* Изменяем курсор на мухобойку */
        }
        #score-container { /* Контейнер для очков */
            margin-top: 20px;
            font-size: 24px;
        }
        #message { /* Контейнер для сообщений (игра окончена) */
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            font-size: 36px;
            display: none; /* Скрываем сообщение по умолчанию */
            background-color: rgba(0, 0, 0, 0.7); /* Полупрозрачный фон для сообщения */
            padding: 20px;
            border-radius: 10px;
        }
        button { /* Стиль для кнопки рестарта */
            margin-top: 20px;
            padding: 10px 20px;
            font-family: 'Press Start 2P', cursive;
            background-color: #4CAF50; /* Зеленый цвет */
            border: none;
            color: white;
            text-align: center;
            text-decoration: none;
            display: inline-block;
            font-size: 16px;
            cursor: pointer;
            border-radius: 5px; /* Закругление углов кнопки */
        }
        button:hover {
            background-color: #45a049; /* Темнее зеленый при наведении */
        }
    </style>
    <link href="https://fonts.googleapis.com/css2?family=Press+Start+2P&display=swap" rel="stylesheet"> <!-- Подключение шрифта -->
</head>
<body>
    <h1>Мухобойка 8-bit</h1>
    <p>Лови мух мухобойкой! Кликни, чтобы ударить.</p>
    <canvas id="gameCanvas" width="600" height="400"></canvas>
    <div id="score-container">Очки: <span id="score">0</span></div>
    <div id="message">
        Игра окончена! Счет: <span id="final-score">0</span>
        <button id="restart-button">Рестарт</button>
    </div>

    <script>
        const canvas = document.getElementById("gameCanvas");
        const ctx = canvas.getContext("2d");
        let score = 0;
        let fly = { x: Math.random() * 560, y: Math.random() * 360, speed: 2 }; // Начальные параметры мухи
        let flyImage = new Image();
        flyImage.src = 'https://i.imgur.com/F2K4R6J.png'; // Изображение мухи
        let swatterImage = new Image();
        swatterImage.src = 'https://i.imgur.com/YhTG8xZ.png'; // Изображение мухобойки
        let hitSound = new Audio('https://www.myinstants.com/media/sounds/slap.mp3'); // Звук попадания
        let missSound = new Audio('https://www.myinstants.com/media/sounds/miss.mp3'); // Звук промаха

        let mouseX = 0, mouseY = 0; // Позиция мыши
        let gameOver = false; // Флаг состояния игры
        let moveFlyInterval; // Переменная для хранения интервала движения мухи

        function drawFly() {
            ctx.clearRect(0, 0, canvas.width, canvas.height); // Очистка канваса перед каждым кадром
            ctx.drawImage(flyImage, fly.x, fly.y, 40, 40); // Рисуем муху
            ctx.drawImage(swatterImage, mouseX - 20, mouseY - 20, 40, 40); // Рисуем мухобойку на месте курсора
        }

        function moveFly() {
            if (gameOver) return; // Прекращаем движение, если игра окончена

            // Случайное движение мухи
            fly.x += (Math.random() - 0.5) * fly.speed * 2;
            fly.y += (Math.random() - 0.5) * fly.speed * 2;

            // Ограничение позиции мухи в пределах канваса
            fly.x = Math.max(0, Math.min(fly.x, canvas.width - 40));
            fly.y = Math.max(0, Math.min(fly.y, canvas.height - 40));
            drawFly(); // Отрисовка мухи и мухобойки
        }

        function hitFly(event) {
            if (gameOver) return; // Не обрабатываем клики, если игра окончена

            const rect = canvas.getBoundingClientRect(); // Получаем координаты канваса относительно окна браузера
            mouseX = event.clientX - rect.left; // Получаем X координату мыши относительно канваса
            mouseY = event.clientY - rect.top; // Получаем Y координату мыши относительно канваса

            // Проверка попадания по мухе
            if (mouseX >= fly.x && mouseX <= fly.x + 40 && mouseY >= fly.y && mouseY <= fly.y + 40) {
                score++; // Увеличиваем счет
                document.getElementById("score").textContent = score; // Обновляем отображение счета
                fly = { x: Math.random() * 560, y: Math.random() * 360, speed: Math.min(fly.speed + 0.2, 7) }; // Увеличиваем скорость мухи и меняем позицию
                hitSound.play(); // Проигрываем звук попадания
            } else {
                missSound.play(); // Проигрываем звук промаха
            }
        }

        // Обработчик движения мыши для перемещения мухобойки
        canvas.addEventListener("mousemove", (event) => {
            const rect = canvas.getBoundingClientRect();
            mouseX = event.clientX - rect.left;
            mouseY = event.clientY - rect.top;
        });

        canvas.addEventListener("click", hitFly); // Обработчик кликов для "удара" мухобойкой
        moveFlyInterval = setInterval(moveFly, 50); // Запускаем движение мухи каждые 50 миллисекунд
        drawFly(); // Первоначальная отрисовка

        // Функция проверки окончания игры
        function checkGameOver() {
            if (score >= 20) { // Условие окончания игры (например, 20 пойманных мух)
                gameOver = true; // Устанавливаем флаг окончания игры
                clearInterval(moveFlyInterval); // Останавливаем движение мухи
                document.getElementById("message").style.display = "block"; // Показываем сообщение об окончании игры
                document.getElementById("final-score").textContent = score; // Выводим финальный счет в сообщении
            }
        }

        setInterval(checkGameOver, 100); // Проверяем условие Game Over каждые 100 мс

        // Обработчик кнопки "Рестарт"
        document.getElementById("restart-button").addEventListener("click", () => {
            score = 0; // Сбрасываем счет
            document.getElementById("score").textContent = 0; // Обновляем отображение счета
            fly = { x: Math.random() * 560, y: Math.random() * 360, speed: 2 }; // Возвращаем начальные параметры мухи
            gameOver = false; // Сбрасываем флаг окончания игры
            document.getElementById("message").style.display = "none"; // Скрываем сообщение об окончании игры
            moveFlyInterval = setInterval(moveFly, 50); // Запускаем движение мухи снова
        });

    </script>
</body>
</html>
