
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
        h1 {
            display: flex;
            justify-content: center;
            align-items: center;
            gap: 10px;
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
        #message, #start-screen {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            font-size: 24px;
            display: none;
            background-color: rgba(0, 0, 0, 0.7);
            padding: 20px;
            border-radius: 10px;
            text-align: center;
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
        #start-screen {
            display: block;
        }
    </style>
    <link href="https://fonts.googleapis.com/css2?family=Press+Start+2P&display=swap" rel="stylesheet">
</head>
<body>
    <h1>Мухобойка 8-битная 🪰🔨</h1>
    <p>Лови мух мухобойкой! Нажмите, чтобы ударить.</p>
    <canvas id="gameCanvas" width="600" height="400"></canvas>
    <div id="score-container">Очки: <span id="score">0</span></div>
    <div id="timer-container">⏳ Время: <span id="timer">30</span> сек</div>
    <div id="message">
        Игра окончена! Счет: <span id="final-score">0</span>
        <button id="restart-button">Перезапустить</button>
    </div>
    <div id="start-screen">
        <button id="start-button">Начать игру</button>
    </div>
    <script>
        const canvas = document.getElementById("gameCanvas");
        const ctx = canvas.getContext("2d");
        let score = 0;
        let timeLeft = 30;
        let gameOver = false;
        let paused = false;
        let gameStarted = false;
        // Разные виды мух 🪰
        const flyImages = [
            'https://i.imgur.com/F2K4R6J.png',
            'https://i.imgur.com/a6yXNvJ.png',
            'https://i.imgur.com/ZvXxVvC.png'
        ];
        let flyImage = new Image();
        flyImage.src = flyImages[Math.floor(Math.random() * flyImages.length)];
        let swatterImage = new Image();
        swatterImage.src = 'https://i.imgur.com/YhTG8xZ.png';
        // Звуки
        let hitSound = new Audio('https://www.fesliyanstudios.com/play-mp3/4385'); // Звук битого стекла
        let missSound = new Audio('https://www.myinstants.com/media/sounds/wrong-buzzer-sound-effect.mp3'); // Звук промаха изменен на "buzzer"
        let bgMusic = new Audio('https://www.myinstants.com/media/sounds/8-bit-music.mp3');
        bgMusic.loop = true;
        let fly = { x: Math.random() * 560, y: Math.random() * 360, speed: 2 };
        let mouseX = 0, mouseY = 0;
        let swatterAnimation = false;
        function drawFly() {
            ctx.clearRect(0, 0, canvas.width, canvas.height);
            ctx.drawImage(flyImage, fly.x, fly.y, 40, 40);
            if (swatterAnimation) {
                ctx.save();
                ctx.translate(mouseX, mouseY);
                ctx.rotate(-0.3);
                ctx.drawImage(swatterImage, -20, -20, 40, 40);
                ctx.restore();
                setTimeout(() => {
                    swatterAnimation = false;
                    drawFly();
                }, 100);
            } else {
                ctx.drawImage(swatterImage, mouseX - 20, mouseY - 20, 40, 40);
            }
        }
        function moveFly() {
            if (gameOver || paused || !gameStarted) return;
            fly.x += (Math.random() - 0.5) * fly.speed * 2;
            fly.y += (Math.random() - 0.5) * fly.speed * 2;
            fly.x = Math.max(0, Math.min(fly.x, canvas.width - 40));
            fly.y = Math.max(0, Math.min(fly.y, canvas.height - 40));
            drawFly();
        }
        function hitFly(event) {
            if (gameOver || paused || !gameStarted) return;
            const rect = canvas.getBoundingClientRect();
            mouseX = event.clientX - rect.left;
            mouseY = event.clientY - rect.top;
            if (mouseX >= fly.x && mouseX <= fly.x + 40 && mouseY >= fly.y && mouseY <= fly.y + 40) {
                score++;
                document.getElementById("score").textContent = score;
                fly = { x: Math.random() * 560, y: Math.random() * 360, speed: Math.min(fly.speed + 0.2, 7) };
                flyImage.src = flyImages[Math.floor(Math.random() * flyImages.length)];
                hitSound.play();
            } else {
                missSound.play();
            }
            swatterAnimation = true;
            drawFly();
        }
        function countdown() {
            if (timeLeft > 0 && !paused && gameStarted) {
                timeLeft--;
                document.getElementById("timer").textContent = timeLeft;
            } else if (timeLeft <= 0) {
                gameOver = true;
                clearInterval(flyInterval);
                clearInterval(timerInterval);
                document.getElementById("message").style.display = "block";
                document.getElementById("final-score").textContent = score;
                bgMusic.pause();
            }
        }
        canvas.addEventListener("mousemove", (event) => {
            const rect = canvas.getBoundingClientRect();
            mouseX = event.clientX - rect.left;
            mouseY = event.clientY - rect.top;
        });
        canvas.addEventListener("click", hitFly);
        canvas.addEventListener("touchstart", (event) => {
            event.preventDefault();
            const touch = event.touches[0];
            const rect = canvas.getBoundingClientRect();
            mouseX = touch.clientX - rect.left;
            mouseY = touch.clientY - rect.top;
            hitFly(event);
        });
        document.addEventListener("keydown", (event) => {
            if (event.key === "p" || event.key === "P") {
                paused = !paused;
                if (paused) {
                    bgMusic.pause();
                } else {
                    bgMusic.play();
                }
            }
        });
        document.getElementById("restart-button").addEventListener("click", () => {
            score = 0;
            timeLeft = 30;
            document.getElementById("score").textContent = score;
            document.getElementById("timer").textContent = timeLeft;
            fly = { x: Math.random() * 560, y: Math.random() * 360, speed: 2 };
            gameOver = false;
            document.getElementById("message").style.display = "none";
            flyInterval = setInterval(moveFly, 50);
            timerInterval = setInterval(countdown, 1000);
            bgMusic.play();
        });
        document.getElementById("start-button").addEventListener("click", () => {
            gameStarted = true;
            document.getElementById("start-screen").style.display = "none";
            bgMusic.play();
        });
        let flyInterval = setInterval(moveFly, 50);
        let timerInterval = setInterval(countdown, 1000);
    </script>
</body>
</html>
