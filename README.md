
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
        }
    </style>
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
        let fly = { x: Math.random() * 560, y: Math.random() * 360, speed: 2 };
        let flyImage = new Image();
        flyImage.src = 'https://i.imgur.com/F2K4R6J.png';
        let swatterImage = new Image();
        swatterImage.src = 'https://i.imgur.com/YhTG8xZ.png';
        let hitSound = new Audio('https://www.myinstants.com/media/sounds/slap.mp3');
        let missSound = new Audio('https://www.myinstants.com/media/sounds/miss.mp3');

        let mouseX = 0, mouseY = 0;
        let gameOver = false; // Флаг для состояния игры

        function drawFly() {
            ctx.clearRect(0, 0, canvas.width, canvas.height);
            ctx.drawImage(flyImage, fly.x, fly.y, 40, 40);
            ctx.drawImage(swatterImage, mouseX - 20, mouseY - 20, 40, 40);
        }

        function moveFly() {
            if (gameOver) return; // Прекращаем движение, если игра окончена

            fly.x += (Math.random() - 0.5) * fly.speed * 2;
            fly.y += (Math.random() - 0.5) * fly.speed * 2;
            fly.x = Math.max(0, Math.min(fly.x, canvas.width - 40));
            fly.y = Math.max(0, Math.min(fly.y, canvas.height - 40));
            drawFly();
        }

        function hitFly(event) {
            if (gameOver) return; // Не обрабатываем клики, если игра окончена

            const rect = canvas.getBoundingClientRect();
            mouseX = event.clientX - rect.left;
            mouseY = event.clientY - rect.top;

            if (mouseX >= fly.x && mouseX <= fly.x + 40 && mouseY >= fly.y && mouseY <= fly.y + 40) {
                score++;
                document.getElementById("score").textContent = score;
                fly = { x: Math.random() * 560, y: Math.random() * 360, speed: Math.min(fly.speed + 0.1, 5) };
                hitSound.play();
            } else {
                missSound.play();
            }
        }

        canvas.addEventListener("mousemove", (event) => {
            const rect = canvas.getBoundingClientRect();
            mouseX = event.clientX - rect.left;
            mouseY = event.clientY - rect.top;
        });

        canvas.addEventListener("click", hitFly);
        setInterval(moveFly, 50);
        drawFly();


        // Добавляем обработку Game Over
        function checkGameOver() {
            if (score >= 20) { // Например, игра заканчивается при достижении 20 очков
                gameOver = true;
                clearInterval(moveFlyInterval); // Останавливаем движение мухи
                document.getElementById("message").style.display = "block"; // Показываем сообщение
                document.getElementById("final-score").textContent = score; // Выводим счет
            }
        }

        setInterval(checkGameOver, 100); // Проверяем Game Over каждые 100 мс

        // Добавляем кнопку "Рестарт"
        document.getElementById("restart-button").addEventListener("click", () => {
            score = 0;
            document.getElementById("score").textContent = 0;
            fly = { x: Math.random() * 560, y: Math.random() * 360, speed: 2 };
            gameOver = false;
            document.getElementById("message").style.display = "none";
            moveFlyInterval = setInterval(moveFly, 50); // Запускаем движение мухи снова
        });

        let moveFlyInterval = setInterval(moveFly, 50); // Переменная для хранения интервала

    </script>
</body>
</html>
