
<html>
<head>
    <title>Змейка ловит мышей</title>
    <HTA:APPLICATION APPLICATIONNAME="SnakeGame" BORDER="thin" SCROLL="no" SINGLEINSTANCE="yes" />
    <style>
        body {
            margin: 0;
            overflow: hidden;
            background: #a0d2eb; /* Голубой фон, как небо в городе */
        }
        canvas {
            display: block;
        }
    </style>
</head>
<body>
    <canvas id="gameCanvas"></canvas>
    <script>
        let canvas = document.getElementById("gameCanvas");
        let ctx = canvas.getContext("2d");
        canvas.width = window.innerWidth;
        canvas.height = window.innerHeight;
        
        let snake = [{x: canvas.width / 2, y: canvas.height / 2}];
        let food = {x: Math.random() * canvas.width, y: Math.random() * canvas.height};
        let snakeSize = 20;
        let speed = 2;
        let dx = speed;
        let dy = 0;
        let foodSpeed = 2;
        let foodDirX = (Math.random() - 0.5) * foodSpeed;
        let foodDirY = (Math.random() - 0.5) * foodSpeed;
        
        document.addEventListener("keydown", function(event) {
            switch(event.key) {
                case "ArrowUp":
                    if (dy === 0) { dy = -speed; dx = 0; }
                    break;
                case "ArrowDown":
                    if (dy === 0) { dy = speed; dx = 0; }
                    break;
                case "ArrowLeft":
                    if (dx === 0) { dx = -speed; dy = 0; }
                    break;
                case "ArrowRight":
                    if (dx === 0) { dx = speed; dy = 0; }
                    break;
            }
        });
        
        function gameLoop() {
            ctx.clearRect(0, 0, canvas.width, canvas.height);
            
            let head = {x: snake[0].x + dx, y: snake[0].y + dy};
            snake.unshift(head);
            
            if (Math.hypot(head.x - food.x, head.y - food.y) < snakeSize) {
                food.x = Math.random() * canvas.width;
                food.y = Math.random() * canvas.height;
                foodDirX = (Math.random() - 0.5) * foodSpeed;
                foodDirY = (Math.random() - 0.5) * foodSpeed;
            } else {
                snake.pop();
            }
            
            food.x += foodDirX;
            food.y += foodDirY;
            if (food.x < 0 || food.x > canvas.width) foodDirX *= -1;
            if (food.y < 0 || food.y > canvas.height) foodDirY *= -1;
            
            ctx.fillStyle = "green";
            snake.forEach(segment => {
                ctx.fillRect(segment.x, segment.y, snakeSize, snakeSize);
            });
            
            ctx.fillStyle = "white";
            ctx.beginPath();
            ctx.arc(food.x, food.y, 10, 0, Math.PI * 2);
            ctx.fill();
            ctx.strokeStyle = "black";
            ctx.lineWidth = 2;
            ctx.stroke();
            
            requestAnimationFrame(gameLoop);
        }
        
        gameLoop();
    </script>
</body>
</html>
