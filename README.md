<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <title>Мухобойка 8-бит 🪰🔨</title>
    <style>
        body {
            background-color: #222;
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
            cursor: url('https://i.imgur.com/R9zVK52.png'), auto; /* Курсор в виде прицела */
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
    </style>
    <link href="https://fonts.googleapis.com/css2?family=Press+Start+2P&display=swap" rel="stylesheet">
</head>
<body>
    <h1>Мухобойка 8-битная 🪰🔨</h1>
    <canvas id="gameCanvas" width="800" height="600"></canvas>
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
        let fly = { x: Math.random() * (canvas.width - 40), y: Math.random() * (canvas.height - 40) };

        const flyImage = new Image();
        flyImage.src = 'https://i.imgur.com/Qc3pQ2t.png'; // Муха в виде иконки
        
        const swatterImage = new Image();
        swatterImage.src = 'https://i.imgur.com/YhTG8xZ.png';
        
        function drawGame() {
            ctx.clearRect(0, 0, canvas.width, canvas.height);
            ctx.drawImage(flyImage, fly.x, fly.y, 40, 40);
        }
        
        function moveFly() {
            if (gameOver) return;
            fly.x = Math.random() * (canvas.width - 40);
            fly.y = Math.random() * (canvas.height - 40);
            drawGame();
        }
        
        function hitFly(event) {
            if (gameOver) return;
            const rect = canvas.getBoundingClientRect();
            const mouseX = event.clientX - rect.left;
            const mouseY = event.clientY - rect.top;
            if (mouseX >= fly.x && mouseX <= fly.x + 40 && mouseY >= fly.y && mouseY <= fly.y + 40) {
                score++;
                document.getElementById("score").textContent = score;
                moveFly();
            }
        }
        
        function countdown() {
            if (timeLeft > 0) {
                timeLeft--;
                document.getElementById("timer").textContent = timeLeft;
            } else {
                gameOver = true;
                document.getElementById("message").style.display = "block";
                document.getElementById("final-score").textContent = score;
            }
        }
        
        canvas.addEventListener("click", hitFly);
        setInterval(moveFly, 1000);
        setInterval(countdown, 1000);
    </script>
</body>
</html>
