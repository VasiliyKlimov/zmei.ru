<!DOCTYPE html>
<html>
<head>
    <title>Мухобойка 8-bit</title>
    <style>
        body {
            background: url('https://i.imgur.com/Wc4WyKo.png') repeat;
            image-rendering: pixelated;
            text-align: center;
            font-family: 'Press Start 2P', cursive;
            color: white;
        }
        canvas {
            display: block;
            margin: 20px auto;
            border: 3px solid white;
            background: #73c48f;
            image-rendering: pixelated;
        }
    </style>
</head>
<body>
    <h1>Мухобойка 8-bit</h1>
    <p>Лови мух мухобойкой! Кликни, чтобы ударить.</p>
    <canvas id="gameCanvas" width="600" height="400"></canvas>
    <p>Очки: <span id="score">0</span></p>
    <script>
        const canvas = document.getElementById("gameCanvas");
        const ctx = canvas.getContext("2d");
        let score = 0;
        let fly = { x: Math.random() * 560, y: Math.random() * 360, speed: 2 };
        let flyImage = new Image();
        flyImage.src = 'https://i.imgur.com/F2K4R6J.png';
        let swatterImage = new Image();
        swatterImage.src = 'https://i.imgur.com/YhTG8xZ.png';
        let hitSound = new Audio('https://www.fesliyanstudios.com/play-mp3/387');
        
        // Флаг для отслеживания удара
        let isHit = false;

        function drawFly() {
            ctx.clearRect(0, 0, canvas.width, canvas.height);
            ctx.drawImage(flyImage, fly.x, fly.y, 40, 40);
            if (isHit) {
                ctx.drawImage(swatterImage, fly.x, fly.y, 40, 40);
                isHit = false; // Сбрасываем флаг после отрисовки
            }
        }
        
        function moveFly() {
            fly.x += (Math.random() - 0.5) * fly.speed * 10;
            fly.y += (Math.random() - 0.5) * fly.speed * 10;
            fly.x = Math.max(0, Math.min(fly.x, canvas.width - 40));
            fly.y = Math.max(0, Math.min(fly.y, canvas.height - 40));
            drawFly();
        }
        
        function hitFly(event) {
            const rect = canvas.getBoundingClientRect();
            const mouseX = event.clientX - rect.left;
            const mouseY = event.clientY - rect.top;
            
            if (mouseX >= fly.x && mouseX <= fly.x + 40 && mouseY >= fly.y && mouseY <= fly.y + 40) {
                score++;
                document.getElementById("score").textContent = score;
                fly = { x: Math.random() * 560, y: Math.random() * 360, speed: fly.speed + 0.1 };
                hitSound.play();
                isHit = true; // Устанавливаем флаг удара
            }
        }
        
        canvas.addEventListener("click", hitFly);
        setInterval(moveFly, 500);
        drawFly();
    </script>
</body>
</html>
