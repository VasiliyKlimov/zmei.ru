<!DOCTYPE html>
<html>
<head>
    <title>Змейка ловит мышей</title>
    <style>
        /* ... (стили остаются без изменений) */
    </style>
</head>
<body>
    <canvas id="gameCanvas"></canvas>
    <div id="score">Score: 0</div>
    <div id="gameOver">Game Over!<br>Your score: <span>0</span><br><button id="restartButton">Restart</button></div>

    <script>
        const canvas = document.getElementById("gameCanvas");
        const ctx = canvas.getContext("2d");
        // ... (остальные переменные)

        let eatSound = new Audio("eat.mp3");
        eatSound.preload = "auto"; // Предзагрузка звука

        // ... (остальные функции)

        function gameLoop() {
            // ... (основной цикл игры)

            if (Math.hypot(head.x - food.x, head.y - food.y) < snakeSize) {
                eatSound.play(); // Воспроизводим звук здесь!
                // ... (остальная логика поедания)
            }

            if (bonus) {
                ctx.fillStyle = "yellow"; // Или другой цвет для бонуса
                ctx.strokeStyle = "black"; // Добавим обводку
                ctx.lineWidth = 2;
                ctx.beginPath();
                ctx.arc(bonus.x, bonus.y, 10, 0, Math.PI * 2);
                ctx.fill();
                ctx.stroke();

                if (Math.hypot(head.x - bonus.x, head.y - bonus.y) < snakeSize) {
                    // ... (логика получения бонуса)
                    bonus = null; // Убираем бонус после использования
                } else if (Date.now() - bonus.startTime > bonus.duration) { // Проверяем время жизни бонуса
                  bonus = null; // Убираем бонус, если время вышло
                }
            }


            // ... (отрисовка змейки и еды)
            requestAnimationFrame(gameLoop);
        }


        function createBonus() {
            if (!bonus) {
                bonus = {
                    x: Math.random() * canvas.width,
                    y: Math.random() * canvas.height,
                    type: "speedUp",
                    duration: 5000,
                    startTime: Date.now() // Запоминаем время создания бонуса
                };
            }
        }

        // ... (остальной код)

        gameLoop();
    </script>
</body>
</html>
