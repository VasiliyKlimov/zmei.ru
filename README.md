<!DOCTYPE html>
<html>
<head>
    <title>Змейка ловит мышей</title>
    <style>
        body {
            margin: 0;
            overflow: hidden;
            background-color: #222;
            font-family: sans-serif;
            color: white;
        }
        canvas {
            background-color: #333; /* Darker background for the canvas */
        }
        #score {
            position: absolute;
            top: 5px;
            left: 5px;
            font-size: 10px;
        }
        #gameOver {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            font-size: 40px;
            text-align: center;
            display: none;
        }
        #restartButton {
            padding: 5px 10px;
            font-size: 10px;
            background-color: #4CAF50;
            color: white;
            border: none;
            cursor: pointer;
        }
    </style>
</head>
<body>
    <div id="score">Счет: 0</div>
    <canvas id="gameCanvas"></canvas>
    <div id="gameOver">
        Игра окончена!<br>
        Ваш счет: <span id="finalScore"></span><br>
        <button id="restartButton">Перезапустить</button>
    </div>

    <script>
        // --- Setup Canvas ---
        const canvas = document.getElementById('gameCanvas');
        const ctx = canvas.getContext('2d');
        canvas.width = window.innerWidth;
        canvas.height = window.innerHeight;

        // --- Game Variables ---
        const gridSize = 20;  // Size of each grid square
        let snake = [{x: gridSize * 5, y: gridSize * 5}]; // Starting position
        let food = generateFood(); // Food initial position
        let direction = 'right'; // Initial direction
        let score = 0;
        let gameLoopInterval;
        let gameSpeed = 11150; // milliseconds between game updates (lower is faster)
        let isGameOver = false;

        // --- Functions ---

        function generateFood() {
            let foodX, foodY;
            do {
                foodX = Math.floor(Math.random() * (canvas.width / gridSize)) * gridSize;
                foodY = Math.floor(Math.random() * (canvas.height / gridSize)) * gridSize;
            } while (snake.some(segment => segment.x === foodX && segment.y === foodY)); // Ensure food doesn't spawn on snake

            return {x: foodX, y: foodY};
        }

        function draw() {
            ctx.clearRect(0, 0, canvas.width, canvas.height);

            // Draw Snake
            snake.forEach((segment, index) => {
                ctx.fillStyle = index === 0 ? 'lime' : 'green'; // Head is lime, body is green
                ctx.fillRect(segment.x, segment.y, gridSize, gridSize);
                ctx.strokeStyle = 'black'; // Add a black border to each segment
                ctx.strokeRect(segment.x, segment.y, gridSize, gridSize);
            });

            // Draw Food
            ctx.fillStyle = 'red';
            ctx.fillRect(food.x, food.y, gridSize, gridSize);
            ctx.strokeStyle = 'black'; // Add a black border to the food
            ctx.strokeRect(food.x, food.y, gridSize, gridSize);
        }

        function update() {
            if (isGameOver) return;

            const head = {x: snake[0].x, y: snake[0].y};

            // Update snake head position based on direction
            switch (direction) {
                case 'up':
                    head.y -= gridSize;
                    break;
                case 'down':
                    head.y += gridSize;
                    break;
                case 'left':
                    head.x -= gridSize;
                    break;
                case 'right':
                    head.x += gridSize;
                    break;
            }

            // Check for food collision
            if (head.x === food.x && head.y === food.y) {
                score++;
                document.getElementById('score').innerText = 'Счет: ' + score;
                food = generateFood();
                // Snake grows (don't remove the last segment)
            } else {
                // Remove the last segment (snake moves)
                snake.pop();
            }

            // Add new head to the beginning of the snake
            snake.unshift(head);


            // Check for wall collision and self-collision
            if (head.x < 0 || head.x >= canvas.width || head.y < 0 || head.y >= canvas.height || checkSelfCollision()) {
                gameOver();
                return;
            }
        }

        function checkSelfCollision() {
            const head = snake[0];
            for (let i = 1; i < snake.length; i++) {
                if (head.x === snake[i].x && head.y === snake[i].y) {
                    return true;
                }
            }
            return false;
        }

        function gameOver() {
            isGameOver = true;
            clearInterval(gameLoopInterval);
            document.getElementById('finalScore').innerText = score;
            document.getElementById('gameOver').style.display = 'block';
        }

        function resetGame() {
            isGameOver = false;
            snake = [{x: gridSize * 5, y: gridSize * 5}];
            food = generateFood();
            direction = 'right';
            score = 0;
            document.getElementById('score').innerText = 'Счет: 0';
            document.getElementById('gameOver').style.display = 'none';
            clearInterval(gameLoopInterval);
            gameLoopInterval = setInterval(gameLoop, gameSpeed);
        }

        function gameLoop() {
            update();
            draw();
        }

        // --- Event Listeners ---
        document.addEventListener('keydown', (event) => {
            switch (event.key) {
                case 'ArrowUp':
                    if (direction !== 'down') direction = 'up';
                    break;
                case 'ArrowDown':
                    if (direction !== 'up') direction = 'down';
                    break;
                case 'ArrowLeft':
                    if (direction !== 'right') direction = 'left';
                    break;
                case 'ArrowRight':
                    if (direction !== 'left') direction = 'right';
                    break;
            }
        });

        document.getElementById('restartButton').addEventListener('click', resetGame);


        // --- Start the Game ---
        gameLoopInterval = setInterval(gameLoop, gameSpeed);
    </script>
</body>
</html>
