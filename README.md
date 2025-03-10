<html>
<head>
    <title>Hit the Fly!</title>
    <style>
        #gameCanvas { border: 1px solid black; }
        #message { display: none; }
    </style>
</head>
<body>
    <canvas id="gameCanvas" width="400" height="300"></canvas>
    <div>Score: <span id="score">0</span></div>
    <div>Time Left: <span id="timer">30</span></div>
    <div id="message">Game Over! Final Score: <span id="final-score"></span></div>

    <script>
        const canvas = document.getElementById('gameCanvas');
        const ctx = canvas.getContext('2d');

        let score = 0;
        let timeLeft = 30;
        let gameOver = false;
        const fly = { x: Math.random() * (canvas.width - 40), y: Math.random() * (canvas.height - 40) };

        const flyImage = new Image();
        flyImage.src = 'https://i.imgur.com/Qc3pQ2t.png'; // Fly icon

        flyImage.onload = () => {
            drawFly();
            setInterval(countdown, 1000); // Start the timer after the image loads
        };

        const drawFly = () => {
            ctx.clearRect(0, 0, canvas.width, canvas.height);
            ctx.drawImage(flyImage, fly.x, fly.y, 40, 40);
        };

        const moveFly = () => {
            if (gameOver) return;
            fly.x = Math.random() * (canvas.width - 40);
            fly.y = Math.random() * (canvas.height - 40);
            drawFly();
        };

        const hitFly = (event) => {
            if (gameOver) return;
            const rect = canvas.getBoundingClientRect();
            const mouseX = event.clientX - rect.left;
            const mouseY = event.clientY - rect.top;
            if (mouseX >= fly.x && mouseX <= fly.x + 40 && mouseY >= fly.y && mouseY <= fly.y + 40) {
                score++;
                document.getElementById("score").textContent = score;
                moveFly();
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
    </script>
</body>
</html>
