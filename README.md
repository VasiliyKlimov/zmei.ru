
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
    <script>
        const canvas = document.getElementById("gameCanvas");
        const ctx = canvas.getContext("2d");
        let score = 0;
        let timeLeft = 30;
        let gameOver = false;
        let paused = false;

        const flyImage = new Image();
        flyImage.src = 'https://i.imgur.com/F2K4R6J.png';
        const swatterImage = new Image();
        swatterImage.src = 'https://i.imgur.com/YhTG8xZ.png';
        
        const hitSound = new Audio('https://www.myinstants.com/media/sounds/broken-glass.mp3');
        const missSound = new Audio('https://www.myinstants.com/media/sounds/miss.mp3');
        const bgMusic = new Audio('https://www.myinstants.com/media/sounds/8-bit-music.mp3');
        bgMusic.loop = true;
        
        let fly = { x: Math.random() * 560, y: Math.random() * 360, speed: 2 };
        let mouseX = 0, mouseY = 0;
        let swatterAnimation = false;

        function drawFly() {
            ctx.clearRect(0, 0, canvas.width, canvas.height);
            ctx.drawImage(flyImage, fly.x, fly.y, 40, 40);
            ctx.drawImage(swatterImage, mouseX - 20, mouseY - 20, 40, 40);
        }

        function moveFly() {
            if (gameOver || paused) return;
            fly.x += (Math.random() - 0.5) * fly.speed * 2;
            fly.y += (Math.random() - 0.5) * fly.speed * 2;
            fly.x = Math.max(0, Math.min(fly.x, canvas.width - 40));
            fly.y = Math.max(0, Math.min(fly.y, canvas.height - 40));
            drawFly();
        }

        function hitFly(event) {
            if (gameOver || paused) return;
            const rect = canvas.getBoundingClientRect();
            mouseX = event.clientX - rect.left;
            mouseY = event.clientY - rect.top;
            
            if (mouseX >= fly.x && mouseX <= fly.x + 40 && mouseY >= fly.y && mouseY <= fly.y + 40) {
                score++;
                document.getElementById("score").textContent = score;
                fly = { x: Math.random() * 560, y: Math.random() * 360, speed: Math.min(fly.speed + 0.2, 7) };
                hitSound.play();
            } else {
                missSound.play();
            }
            drawFly();
        }

        function countdown() {
            if (timeLeft > 0 && !paused) {
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

        let flyInterval = setInterval(moveFly, 50);
        let timerInterval = setInterval(countdown, 1000);
        bgMusic.play();
    </script>
</body>
</html>
