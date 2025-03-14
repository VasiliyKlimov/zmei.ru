
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <title>Мухобойка 8-бит 🪰🔨</title>
    <style>
        body {
            background: url('https://i.imgur.com/3ZQZQ9m.png') repeat;
            image-rendering: pixelated;
            text-align: center;
            font-family: 'Press Start 2P', cursive;
            color: white;
            overflow: hidden;
            margin: 0;
        }
        canvas {
            display: block;
            margin: 20px auto;
            border: 3px solid white;
            background: #73c48f;
            image-rendering: pixelated;
            cursor: crosshair;
        }
        #score-container, #timer-container {
            margin-top: 10px;
            font-size: 20px;
        }
        #message {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            font-size: 24px;
            display: none;
            background-color: rgba(0, 0, 0, 0.7);
            padding: 20px;
            border-radius: 10px;
        }
        button {
            margin-top: 20px;
            padding: 10px 20px;
            font-family: 'Press Start 2P', cursive;
            background-color: #4CAF50;
            border: none;
            color: white;
            font-size: 16px;
            cursor: pointer;
            border-radius: 5px;
        }
        button:hover {
            background-color: #45a049;
        }
    </style>
    <link href="https://fonts.googleapis.com/css2?family=Press+Start+2P&display=swap" rel="stylesheet">
</head>
<body>
    <h1>Мухобойка 8-битная 🪰🔨</h1>
    <p>Лови мух мухобойкой! Нажмите, чтобы ударить.</p>
    <canvas id="gameCanvas" width="800" height="600"></canvas>
    <div id="score-container">Очки: <span id="score">0</span></div>
    <div id="timer-container">⏳ Время: <span id="timer">30</span> сек</div>
    <div id="message">
        Игра окончена! Счет: <span id="final-score">0</span>
        <button id="restart-button">Перезапустить</button>
    </div>

    <script>
        const canvas = document.getElementById('gameCanvas');
        const ctx = canvas.getContext('2d');

        let score = 0;
        let timeLeft = 30;
        let gameOver = false;
        const fly = { x: Math.random() * (canvas.width - 40), y: Math.random() * (canvas.height - 40), speed: 2 };
        let swatterAnimation = false;

        const flyImage = new Image();
        flyImage.src = 'https://i.imgur.com/Qc3pQ2t.png'; // Fly icon

        const swatterImage = new Image();
        swatterImage.src = 'https://i.imgur.com/YhTG8xZ.png'; // Swatter icon

        const hitSound = new Audio('https://www.myinstants.com/media/sounds/slap.mp3'); // Звук удара
        const missSound = new Audio('https://www.myinstants.com/media/sounds/miss.mp3'); // Звук промаха

        flyImage.onload = () => {
            drawFly();
            setInterval(countdown, 1000); // Start the timer after the image loads
            setInterval(moveFly, 1000); // Move the fly every second
        };

        const drawFly = () => {
            ctx.clearRect(0, 0, canvas.width, canvas.height);
            ctx.drawImage(flyImage, fly.x, fly.y, 40, 40);

            if (swatterAnimation) {
                ctx.save();
                ctx.translate(mouseX, mouseY);
                ctx.rotate(-0.3); // Наклон мухобойки при ударе
                ctx.drawImage(swatterImage, -20, -20, 40, 40);
                ctx.restore();
                setTimeout(() => { swatterAnimation = false; drawFly(); }, 100);
            }
        };

        const moveFly = () => {
            if (gameOver) return;
            fly.x = Math.random() * (canvas.width - 40); // Хаотичное появление по X
            fly.y = Math.random() * (canvas.height - 40); // Хаотичное появление по Y
            drawFly(); // Перерисовываем муху после перемещения
        };

        const hitFly = (event) => {
            if (gameOver) return;
            const rect = canvas.getBoundingClientRect();
            mouseX = event.clientX - rect.left;
            mouseY = event.clientY - rect.top;
            if (mouseX >= fly.x && mouseX <= fly.x + 40 && mouseY >= fly.y && mouseY <= fly.y + 40) {
                score++;
                document.getElementById("score").textContent = score;

                // Обновление времени после 10 очков
                if (score % 10 === 0) {
                    timeLeft += 10; // Добавляем 10 секунд
                    document.getElementById("timer").textContent = timeLeft;
                }

                fly.speed = Math.min(fly.speed + 0.2, 7); // Увеличиваем скорость мухи
                moveFly();
                hitSound.play();
                swatterAnimation = true;
            } else {
                missSound.play();
            }
        };

        const countdown = () => {
            if (timeLeft > 0) {
                timeLeft--;
                document.getElementById("timer").textContent = timeLeft;
            } else {
                gameOver = true;
                document.getElementById("message").style.display = "block";
                document.getElementById("final-score").textContent = score;
            }
        };

        canvas.addEventListener("click", hitFly);

        document.getElementById("restart-button").addEventListener("click", () => {
            score = 0;
            timeLeft = 30;
            document.getElementById("score").textContent = score;
            document.getElementById("timer").textContent = timeLeft;
            fly.x = Math.random() * (canvas.width - 40);
            fly.y = Math.random() * (canvas.height - 40);
            fly.speed = 2;
            gameOver = false;
            document.getElementById("message").style.display = "none";
            drawFly();
        });
    </script>
</body>
</html>
