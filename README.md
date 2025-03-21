<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <title>Мухобойка 8-бит 🪰🔨</title>
    <style>
        body {
            background: url('https://your-image-url.com');
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
        .progress-bar {
            width: 300px;
            height: 15px;
            background-color: #555;
            margin-top: 10px;
            border-radius: 5px;
        }
        .progress-bar-fill {
            width: 100%;
            height: 100%;
            background-color: green;
            border-radius: 5px;
            transition: width 1s linear;
        }
    </style>
    <link href="https://fonts.googleapis.com/css2?family=Press+Start+2P&display=swap" rel="stylesheet">
</head>
<body>
    <h1>Мухобойка 8-битная 🪰🔨</h1>
    <p>Лови мух мухобойкой! Нажми, чтобы ударить.</p>
    <canvas id="gameCanvas" width="800" height="600"></canvas>
    <div class="progress-bar">
        <div id="progress-bar-fill" class="progress-bar-fill"></div>
    </div>
    <div id="score-container">Очки: <span id="score">0</span></div>
    <div id="timer-container">⏳ Время: <span id="timer">30</span> сек</div>
    <div id="message">
        Игра окончена! Счёт: <span id="final-score">0</span><br>
        Лучший результат: <span id="high-score">0</span>
        <button id="restart-button">Перезапустить</button>
    </div>

    <script>
        const canvas = document.getElementById('gameCanvas');
        const ctx = canvas.getContext('2d');

        let score = 0;
        let timeLeft = 30;
        let gameOver = false;
        let difficultyLevel = 1; // Уровень сложности
        let flySpeedMultiplier = 1; // Множитель скорости мухи
        let flySize = 40; // Размер мухи
        let comboCounter = 0; // Счётчик комбо
        let lastHitTime = Date.now(); // Время последнего попадания
        let highScore = localStorage.getItem('highScore') || 0; // Таблица рекордов

        const fly = { x: Math.random() * (canvas.width - 40), y: Math.random() * (canvas.height - 40), speed: 2 };
        let swatterAnimation = false;

        const flyImage = new Image();
        flyImage.src = 'https://i.imgur.com/Qc3pQ2t.png'; // Fly icon

        const swatterImage = new Image();
        swatterImage.src = 'https://i.imgur.com/YhTG8xZ.png'; // Swatter icon

        const hitSound = new Audio('hit-sound.mp3'); // Звук удара
        const missSound = new Audio('miss-sound.mp3'); // Звук промаха
        const bgMusic = new Audio('background-music.mp3'); // Фоновая музыка
        bgMusic.loop = true;
        bgMusic.volume = 0.3; // Громкость фона на 30%

        flyImage.onload = () => {
            drawFly();
            setInterval(countdown, 1000); // Таймер начинается после загрузки изображений
            setInterval(moveFly, 1000 / difficultyLevel); // Перемещение мухи каждые 1000/уровень сложности
            updateProgressBar(); // Обновление индикатора времени
        };

        const drawFly = () => {
            ctx.clearRect(0, 0, canvas.width, canvas.height);
            ctx.drawImage(flyImage, fly.x, fly.y, flySize, flySize);

            if (swatterAnimation) {
                ctx.save();
                ctx.translate(mouseX, mouseY);
                ctx.rotate(-0.3); // Наклон мухобойки при ударе
                ctx.drawImage(swatterImage, -flySize / 2, -flySize / 2, flySize, flySize);
                ctx.restore();
                setTimeout(() => { swatterAnimation = false; drawFly(); }, 100);
            }
        };

        const moveFly = () => {
            if (gameOver) return;
            fly.x = Math.random() * (canvas.width - flySize); // Случайное перемещение по X
            fly.y = Math.random() * (canvas.height - flySize); // Случайное перемещение по Y
            drawFly(); // Перерисовка мухи после перемещения
        };

        const hitFly = (event) => {
            if (gameOver) return;
            const rect = canvas.getBoundingClientRect();
            mouseX = event.clientX - rect.left;
            mouseY = event.clientY - rect.top;
            if (mouseX >= fly.x && mouseX <= fly.x + flySize &&
                mouseY >= fly.y && mouseY <= fly.y + flySize) {
                score++;
                document.getElementById("score").textContent = score;

                // Бонус за комбо
                comboCounter++;
                if ((Date.now() - lastHitTime) < 500) { // Комбо за быстрый удар
                    score += 2;
                    document.getElementById("score").textContent = score;
                }
                lastHitTime = Date.now(); // Обновляем время последнего удара

                // Увеличение скорости мухи
                flySpeedMultiplier = Math.min(flySpeedMultiplier + 0.05, 3);
                fly.size = Math.min(flySize + 10, 80); // Увеличение размера мухи

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
                updateProgressBar(); // Обновление индикатора времени
            } else {
                gameOver = true;
                document.getElementById("message").style.display = "block";
                document.getElementById("final-score").textContent = score;
                document.getElementById("high-score").textContent = highScore;
                saveHighScore(score); // Сохранение лучшего результата
            }
        };

        const updateProgressBar = () => {
            const progressBarFill = document.getElementById('progress-bar-fill');
            progressBarFill.style.width = `${(timeLeft / 30) * 100}%`;
        };

        const saveHighScore = (newScore) => {
            if (newScore > highScore) {
                highScore = newScore;
                localStorage.setItem('highScore', highScore);
            }
        };

        canvas.addEventListener("click", hitFly);

        document.getElementById("restart-button").addEventListener("click", () => {
            score = 0;
            timeLeft = 30;
            difficultyLevel = 1;
            flySpeedMultiplier = 1;
            flySize = 40;
            comboCounter = 0;
            lastHitTime = Date.now();
            gameOver = false;
            document.getElementById("score").textContent = score;
            document.getElementById("timer").textContent = timeLeft;
            document.getElementById("message").style.display = "none";
            drawFly();
        });
    </script>
</body>
</html>
