<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Убей Муху</title>
    <style>
        /* --- Стили для страницы --- */
        body {
            display: flex;              /* Используем Flexbox для центрирования */
            justify-content: center;    /* Центрирование по горизонтали */
            align-items: center;        /* Центрирование по вертикали */
            flex-direction: column;     /* Элементы располагаются друг под другом */
            min-height: 100vh;          /* Минимальная высота равна высоте экрана */
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; /* Выбираем шрифт */
            background-color: #f0f0f0; /* Светло-серый фон */
            margin: 0;                  /* Убираем отступы по умолчанию у body */
        }

        h1 {
            color: #333;                /* Темный цвет заголовка */
        }

        /* --- Стили для контейнера игры --- */
        #gameContainer {
            position: relative;         /* Нужно для позиционирования message */
            border: 2px solid #555;     /* Темно-серая рамка */
            background-color: #e0f7fa; /* Светло-голубой фон канваса */
            width: 500px;               /* Ширина игрового поля */
            height: 400px;              /* Высота игрового поля */
            cursor: crosshair;          /* Курсор в виде прицела */
            margin-top: 15px;           /* Отступ сверху */
            box-shadow: 0 4px 8px rgba(0,0,0,0.1); /* Небольшая тень */
        }

        #gameCanvas {
            display: block;             /* Убирает лишний отступ под канвасом */
            width: 100%;                /* Канвас занимает всю ширину контейнера */
            height: 100%;               /* Канвас занимает всю высоту контейнера */
        }

        /* --- Стили для интерфейса (счет, время, рекорд) --- */
        #ui {
            margin-top: 15px;           /* Отступ сверху */
            text-align: center;         /* Текст по центру */
            width: 500px;               /* Ширина блока UI как у канваса */
            padding: 10px;              /* Внутренние отступы */
            background-color: #fff;     /* Белый фон для блока UI */
            border-radius: 8px;         /* Скругленные углы */
            box-shadow: 0 2px 4px rgba(0,0,0,0.1); /* Тень */
        }

        #ui span { /* Стили для текстовых элементов в UI */
            margin: 0 10px;             /* Горизонтальные отступы между элементами */
            font-size: 1.1em;           /* Немного увеличенный шрифт */
            color: #555;                /* Цвет текста */
        }

        #ui span span { /* Стили для самих значений (счет, время, рекорд) */
            font-weight: bold;          /* Жирный шрифт для цифр */
            color: #007bff;             /* Синий цвет для значений */
        }

        /* --- Стили для прогресс-бара времени --- */
        #progress-bar-container {
            width: 100%;                /* На всю ширину UI блока */
            background-color: #ddd;     /* Фон контейнера прогресс-бара */
            height: 12px;               /* Высота прогресс-бара */
            margin-top: 8px;            /* Отступ сверху */
            border-radius: 6px;         /* Скругленные углы */
            overflow: hidden;           /* Скрывает выходящую за рамки заливку */
        }

        #progress-bar-fill {
            height: 100%;               /* Заливка на всю высоту */
            background-color: #28a745;  /* Зеленый цвет заливки */
            width: 100%;                /* Начальная ширина 100% */
            transition: width 0.5s linear; /* Плавный переход ширины */
            border-radius: 6px;         /* Скругление (видно при уменьшении) */
        }

        /* --- Стили для сообщения о конце игры --- */
        #message {
            position: absolute;         /* Абсолютное позиционирование внутри gameContainer */
            top: 50%;                   /* Центрирование по вертикали */
            left: 50%;                  /* Центрирование по горизонтали */
            transform: translate(-50%, -50%); /* Точное центрирование */
            background: rgba(0, 0, 0, 0.8); /* Полупрозрачный темный фон */
            color: white;               /* Белый цвет текста */
            padding: 30px;              /* Увеличенные внутренние отступы */
            border-radius: 10px;        /* Скругленные углы */
            text-align: center;         /* Текст по центру */
            display: none;              /* Изначально скрыто */
            z-index: 10;                /* Поверх канваса */
            box-shadow: 0 5px 15px rgba(0,0,0,0.3); /* Тень для сообщения */
        }

        #message h2 {
            margin-top: 0;              /* Убираем верхний отступ у заголовка */
            color: #ffc107;             /* Желтый цвет для заголовка "Время вышло!" */
        }

        #message p {
            font-size: 1.1em;           /* Размер шрифта для счета */
            margin: 10px 0;             /* Отступы для параграфов */
        }

        /* --- Стили для кнопки перезапуска --- */
        button#restart-button { /* Уточняем селектор для кнопки */
            padding: 12px 25px;         /* Увеличенные отступы кнопки */
            font-size: 1.1em;           /* Увеличенный шрифт кнопки */
            cursor: pointer;            /* Курсор-указатель */
            margin-top: 20px;           /* Отступ сверху */
            background-color: #28a745;  /* Зеленый фон кнопки */
            color: white;               /* Белый текст кнопки */
            border: none;               /* Без рамки */
            border-radius: 5px;         /* Скругленные углы */
            transition: background-color 0.3s ease; /* Плавное изменение фона при наведении */
        }

        button#restart-button:hover {
            background-color: #218838;  /* Более темный зеленый при наведении */
        }

    </style>
</head>
<body>

    <h1>Убей Муху!</h1>

    <div id="ui">
        <span>Счет: <span id="score">0</span></span> |
        <span>Время: <span id="timer">30</span> сек</span> |
        <span>Рекорд: <span id="high-score-display">0</span></span>
        <div id="progress-bar-container">
            <div id="progress-bar-fill"></div>
        </div>
    </div>

    <div id="gameContainer">
        <canvas id="gameCanvas" width="500" height="400"></canvas>

        <div id="message">
            <h2>Время вышло!</h2>
            <p>Ваш счет: <span id="final-score">0</span></p>
            <p>Лучший счет: <span id="high-score-message">0</span></p>
            <button id="restart-button">Играть снова</button>
        </div>
    </div>

    <script src="game.js"></script>

</body>
</html>
