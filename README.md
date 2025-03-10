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
        let fly = { x: Math.random() * (canvas.width - 40), y: Math.random() * (canvas.height - 40) };

        const flyImage = new Image();
        flyImage.src = 'https://i.imgur.com/Qc3pQ2t.png'; // Fly icon

        const swatterImage = new Image();
        swatterImage.src = 'https://i.imgur.com/YhTG8xZ.png';

        flyImage.onload = function() {
            drawGame(); // Initial draw after image loads
            setInterval(countdown, 1000); // Start the timer after the image loads
        };

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
                document.getElementById("message").style.display = "block"; // Or use style.visibility = "visible";
                document.getElementById("final-score").textContent = score;
            }
        }

        canvas.addEventListener("click", hitFly);

    </script>
</body>
</html>
