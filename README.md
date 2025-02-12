<html>
<head>
    <title>Змейка ловит мышей</title>
    <style>
        body {
            margin: 0;
            overflow: hidden;
            background: url('https://i.pinimg.com/560x/e0/9f/9e/e09f9e2954c25921c998589945415592.jpg') no-repeat center center fixed;
            background-size: cover;
            font-family: Arial, sans-serif;
            color: white;
        }
        canvas {
            display: block;
            border: 5px solid black;
            margin: 10px auto;
            background-color: rgba(0, 0, 0, 0.5);
        }
    </style>
</head>
<body>
    <canvas id="gameCanvas"></canvas>
    <script>
        let canvas = document.getElementById("gameCanvas");
        let ctx = canvas.getContext("2d");
        canvas.width = 600;
        canvas.height = 600;
        
        let snake = [{ x: 300, y: 300 }];
        let snakeSize = 20;
        let dx = snakeSize, dy = 0;
        let score = 0;
        let frameRate = 100;
        let gameStarted = false;
        let food = generateFoodPosition();

        let eatSound = new Audio('eat.mp3');
        let crashSound = new Audio('crash.mp3');

        function generateFoodPosition() {
            let newFood;
            do {
                newFood = {
                    x: Math.floor(Math.random() * (canvas.width / snakeSize)) * snakeSize,
                    y: Math.floor(Math.random() * (canvas.height / snakeSize)) * snakeSize
                };
            } while (snake.some(segment => segment.x === newFood.x && segment.y === newFood.y));
            return newFood;
        }

        function changeDirection(event) {
            const { keyCode } = event;
            if (keyCode === 37 && dx === 0) { dx = -snakeSize; dy = 0; }
            if (keyCode === 39 && dx === 0) { dx = snakeSize; dy = 0; }
            if (keyCode === 38 && dy === 0) { dx = 0; dy = -snakeSize; }
            if (keyCode === 40 && dy === 0) { dx = 0; dy = snakeSize; }
        }

        function updateSpeed() {
            frameRate = Math.max(50, 100 - score * 2);
        }

        function gameLoop() {
            if (!gameStarted) return;
            setTimeout(() => {
                let head = { x: snake[0].x + dx, y: snake[0].y + dy };
                if (head.x < 0 || head.x >= canvas.width || head.y < 0 || head.y >= canvas.height ||
                    snake.some(segment => segment.x === head.x && segment.y === head.y)) {
                    crashSound.play();
                    alert("Игра окончена! Ваш результат: " + score);
                    location.reload();
                    return;
                }
                
                if (head.x === food.x && head.y === food.y) {
                    score++;
                    updateSpeed();
                    eatSound.play();
                    food = generateFoodPosition();
                } else {
                    snake.pop();
                }
                
                snake.unshift(head);
                ctx.clearRect(0, 0, canvas.width, canvas.height);
                ctx.fillStyle = "green";
                snake.forEach(segment => ctx.fillRect(segment.x, segment.y, snakeSize, snakeSize));
                
                ctx.fillStyle = "red";
                ctx.fillRect(food.x, food.y, snakeSize, snakeSize);
                
                requestAnimationFrame(gameLoop);
            }, frameRate);
        }

        document.addEventListener('keydown', event => {
            if (!gameStarted) {
                gameStarted = true;
                gameLoop();
            }
            changeDirection(event);
        });

        document.addEventListener('touchstart', handleTouchStart, false);
        document.addEventListener('touchmove', handleTouchMove, false);
        let xDown = null, yDown = null;

        function handleTouchStart(evt) {
            xDown = evt.touches[0].clientX;
            yDown = evt.touches[0].clientY;
        }

        function handleTouchMove(evt) {
            if (!xDown || !yDown) return;
            let xUp = evt.touches[0].clientX;
            let yUp = evt.touches[0].clientY;
            let xDiff = xDown - xUp;
            let yDiff = yDown - yUp;
            if (Math.abs(xDiff) > Math.abs(yDiff)) {
                if (xDiff > 0 && dx === 0) { dx = -snakeSize; dy = 0; }
                else if (xDiff < 0 && dx === 0) { dx = snakeSize; dy = 0; }
            } else {
                if (yDiff > 0 && dy === 0) { dx = 0; dy = -snakeSize; }
                else if (yDiff < 0 && dy === 0) { dx = 0; dy = snakeSize; }
            }
            xDown = null;
            yDown = null;
        }
    </script>
</body>
</html>
