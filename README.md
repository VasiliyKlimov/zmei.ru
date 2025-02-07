<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Snake Game</title>
    <style>
        body {
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            background-color: black;
            margin: 0;
        }
        canvas {
            background-color: #111;
        }
    </style>
</head>
<body>
    <canvas id="gameCanvas" width="400" height="400"></canvas>
    <script>
        const canvas = document.getElementById("gameCanvas");
        const ctx = canvas.getContext("2d");

        const gridSize = 20;
        let snake = [{ x: 200, y: 200 }];
        let direction = { x: 0, y: 0 };
        let food = { x: 100, y: 100 };

        function drawRect(x, y, color) {
            ctx.fillStyle = color;
            ctx.fillRect(x, y, gridSize, gridSize);
        }

        function update() {
            if (direction.x === 0 && direction.y === 0) return; // Prevent moving before key press
            let head = { x: snake[0].x + direction.x * gridSize, y: snake[0].y + direction.y * gridSize };
            snake.unshift(head);
            if (head.x === food.x && head.y === food.y) {
                food = {
                    x: Math.floor(Math.random() * (canvas.width / gridSize)) * gridSize,
                    y: Math.floor(Math.random() * (canvas.height / gridSize)) * gridSize
                };
            } else {
                snake.pop();
            }
        }

        function draw() {
            ctx.clearRect(0, 0, canvas.width, canvas.height);
            snake.forEach((segment, index) => {
                drawRect(segment.x, segment.y, index === 0 ? "red" : "green");
            });
            drawRect(food.x, food.y, "white");
        }

        function gameLoop() {
            update();
            draw();
        }

        setInterval(gameLoop, 100);

        document.addEventListener("keydown", (event) => {
            switch (event.key) {
                case "ArrowUp": if (direction.y === 0) direction = { x: 0, y: -1 }; break;
                case "ArrowDown": if (direction.y === 0) direction = { x: 0, y: 1 }; break;
                case "ArrowLeft": if (direction.x === 0) direction = { x: -1, y: 0 }; break;
                case "ArrowRight": if (direction.x === 0) direction = { x: 1, y: 0 }; break;
            }
        });
    </script>
</body>
</html>
