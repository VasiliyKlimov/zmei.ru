
<html>
<head>
    <title>Змейка ловит мышей</title>
    <HTA:APPLICATION APPLICATIONNAME="SnakeGame" BORDER="thin" SCROLL="no" SINGLEINSTANCE="yes" />
    <style>
        body {
            margin: 0;
            overflow: hidden;
            background: url('https://source.unsplash.com/800x600/?snake') no-repeat center center;
            background-size: cover;
        }
        canvas {
            display: block;
            border: 5px solid black;
        }
        #scoreboard {
            position: absolute;
            top: 10px;
            left: 10px;
            font-size: 20px;
            font-weight: bold;
            font-family: Arial, sans-serif;
            color: black;
            background: rgba(255, 255, 255, 0.7);
            padding: 5px;
            border-radius: 5px;
        }
        #restartButton {
            position: absolute;
            top: 50px;
            left: 10px;
            font-size: 16px;
            padding: 5px 10px;
            cursor: pointer;
        }
    </style>
</head>
<body>
    <div id="scoreboard">
        Лучший результат: <span id="bestScore">0</span><br>
        Текущий результат: <span id="score">0</span>
    </div>
    <button id="restartButton" onclick="restartGame()">Перезапуск</button>
    <canvas id="gameCanvas"></canvas>
    <script>
        let canvas = document.getElementById("gameCanvas");
        let ctx = canvas.getContext("2d");
        canvas.width = 800;
        canvas.height = 600;
        
        let snake = [{x: 400, y: 300}];
        let food = {x: Math.random() * (canvas.width - 20), y: Math.random() * (canvas.height - 20)};
        let snakeSize = 20;
        let speed = 20;
        let dx = speed;
        let dy = 0;
        let score = 0;
        let bestScore = localStorage.getItem("bestScore") || 0;
        let frameRate = 100;

        document.getElementById("bestScore").innerText = bestScore;

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
            setTimeout(() => {
                ctx.clearRect(0, 0, canvas.width, canvas.height);
                
                let head = {x: snake[0].x + dx, y: snake[0].y + dy};
                
                if (head.x < 0 || head.x >= canvas.width || head.y < 0 || head.y >= canvas.height) {
                    if (score > bestScore) {
                        bestScore = score;
                        localStorage.setItem("bestScore", bestScore);
                    }
                    alert("Игра окончена! Ваш результат: " + score);
                    restartGame();
                    return;
                }
                
                snake.unshift(head);
                
                if (Math.hypot(head.x - food.x, head.y - food.y) < snakeSize) {
                    score++;
                    document.getElementById("score").innerText = score;
                    food.x = Math.random() * (canvas.width - 20);
                    food.y = Math.random() * (canvas.height - 20);
                    if (score % 100 === 0) frameRate *= 0.9;
                } else {
                    snake.pop();
                }
                
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
            }, frameRate);
        }
        
        function restartGame() {
            score = 0;
            document.getElementById("score").innerText = score;
            snake = [{x: 400, y: 300}];
            dx = speed;
            dy = 0;
            frameRate = 100;
            gameLoop();
        }

        gameLoop();
    </script>
</body>
</html>
