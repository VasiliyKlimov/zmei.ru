
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
            padding: 10px; /* Увеличиваем padding для лучшего вида */
            border-radius: 5px;
            display: flex; /* Используем flexbox для вертикального расположения */
            flex-direction: column; /* Размещаем элементы в колонку */
            align-items: flex-start; /* Выравниваем элементы по левому краю */
        }
        #scoreboard > div { /* Стиль для каждого элемента scoreboard */
            margin-bottom: 5px; /* Отступ между элементами */
        }
        #restartButton {
            margin-top: 10px; /* Отступ сверху от scoreboard */
            font-size: 16px;
            padding: 5px 10px;
            cursor: pointer;
        }
        #highScoresList {
            margin-top: 20px;
            color: black;
            background: rgba(255, 255, 255, 0.7);
            padding: 10px;
            border-radius: 5px;
            max-height: 300px; /* Ограничиваем высоту списка */
            overflow-y: auto; /* Добавляем прокрутку, если список длинный */
        }
        #highScoresList li {
            margin-bottom: 5px;
        }

    </style>
</head>
<body>
    <div id="scoreboard">
        <div>Лучший результат: <span id="bestScore">0</span></div>
        <div>Текущий результат: <span id="score">0</span></div>
        <button id="restartButton" onclick="restartGame()">Перезапуск</button>
        <div id="highScores">
            <h3>Рекорды:</h3>
            <ol id="highScoresList">
                </ol>
        </div>
    </div>
    <canvas id="gameCanvas"></canvas>
    <script>
        // ... (остальной код игры)

        let highScores = JSON.parse(localStorage.getItem("highScores")) || [];

        function updateHighScores() {
            highScores.push(score);
            highScores.sort((a, b) => b - a); // Сортируем по убыванию
            highScores = highScores.slice(0, 500); // Оставляем только топ-500

            localStorage.setItem("highScores", JSON.stringify(highScores));
            renderHighScores();
        }

        function renderHighScores() {
            const highScoresList = document.getElementById("highScoresList");
            highScoresList.innerHTML = ""; // Очищаем список

            highScores.forEach((score, index) => {
                const li = document.createElement("li");
                li.textContent = `${index + 1}. ${score}`;
                highScoresList.appendChild(li);
            });
        }

        renderHighScores(); // Выводим рекорды при загрузке страницы


        function gameLoop() {
            setTimeout(() => {
                // ... (основной цикл игры)

                if (Math.hypot(head.x - food.x, head.y - food.y) < snakeSize) {
                    // ... (логика съедания еды)
                    updateHighScores(); // Обновляем список рекордов
                }

                // ... (отрисовка)
                requestAnimationFrame(gameLoop);
            }, frameRate);
        }

        function restartGame() {
            // ... (логика перезапуска)
            renderHighScores(); // Обновляем список рекордов после перезапуска
        }

        // ... (остальной код)

    </script>
</body>
</html>
