<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <title>Мухобойка 8-бит 🪰🔨</title>
    <style>
        body {
            background: url('https://i.imgur.com/3ZQZQ9m.png') repeat;
            image-rendering: pixelated;
            text-align: center;
            font-family: 'Press Start 2P', cursive;
            color: white;
            overflow: hidden;
            margin: 0;
        }
        h1 {
            display: flex;
            justify-content: center;
            align-items: center;
            gap: 10px;
        }
        canvas {
            display: block;
            margin: 20px auto;
            border: 3px solid white;
            background: #73c48f;
            image-rendering: pixelated;
            cursor: crosshair;
        }
        #score-container, #timer-container {
            margin-top: 10px;
            font-size: 20px;
        }
        #message {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            font-size: 24px;
            display: none;
            background-color: rgba(0, 0, 0, 0.7);
            padding: 20px;
            border-radius: 10px;
        }
        button {
            margin-top: 20px;
            padding: 10px 20px;
            font-family: 'Press Start 2P', cursive;
            background-color: #4CAF50;
            border: none;
            color: white;
            font-size: 16px;
            cursor: pointer;
            border-radius: 5px;
        }
        button:hover {
            background-color: #45a049;
        }
        #start-screen {
            display: block;
        }
    </style>
    <link href="https://fonts.googleapis.com/css2?family=Press+Start+2P&display=swap" rel="stylesheet">
</head>
<body>
    <h1>Мухобойка 8-битная 🪰🔨</h1>
    <p>Лови мух мухобойкой! Нажмите, чтобы ударить.</p>
    <canvas id="gameCanvas" width="800" height="600"></canvas> <!-- Изменены размеры холста -->
    <div id="score-container">Очки: <span id="score">0</span></div>
    <div id="timer-container">⏳ Время: <span id="timer">30</span> сек</div>
    <div id="message">
        Игра окончена! Счет: <span id="final-score">0</span>
        <button id="restart-button">Перезапустить</button>
    </div>
    <div id="start-screen">
        <button id="start-button">Начать игру</button>
    </div>
    <script>
        const canvas = document.getElementById("gameCanvas");
        const ctx = canvas.getContext("2d");

        let score = 0;
        let timeLeft = 30;
        let gameOver = false;
        let paused = false;
        let gameStarted = false;

        // Разные виды мух 🪰
        const flyImages = [
            'https://i.imgur.com/F2K4R6J.png',
            'https://i.imgur.com/a6yXNvJ.png',
            'https://i.imgur.com/ZvXxVvC.png'
        ];

        let flyImage = new Image();
        flyImage.src = flyImages[Math.floor(Math.random() * flyImages.length)];

        let swatterImage = new Image();
        swatterImage.src = 'https://i.imgur.com/YhTG8xZ.png';

        // Звуки
        let hitSound = new Audio('https://www.myinstants.com/media/sounds/cartoon-bird-whistle.mp3'); // Новый звук удара
        let missSound = new Audio('https://www.fesliyanstudios.com/play-mp3/1257'); // Звук промаха
        let bgMusic = new Audio('https://www.myinstants.com/media/sounds/8-bit-music.mp3');
        bgMusic.loop = true;

        let fly = { x: Math.random() * 760, y: Math.random() * 560, speed: 2 }; // Изменены координаты для нового размера холста
