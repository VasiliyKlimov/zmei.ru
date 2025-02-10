<!DOCTYPE html>
<html>
<head>
    <title>3D Змейка</title>
    <style>
        body { margin: 0; overflow: hidden; }
        canvas { display: block; }
    </style>
</head>
<body>
    <script src="https://cdn.jsdelivr.net/npm/three@latest/build/three.min.js"></script>
    <script>
        // 1. Настройка сцены
        const scene = new THREE.Scene();
        const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
        const renderer = new THREE.WebGLRenderer();
        renderer.setSize(window.innerWidth, window.innerHeight);
        document.body.appendChild(renderer.domElement);

        // Освещение
        const ambientLight = new THREE.AmbientLight(0x404040);
        scene.add(ambientLight);
        const directionalLight = new THREE.DirectionalLight(0xffffff, 0.5);
        directionalLight.position.set(1, 1, 1).normalize();
        scene.add(directionalLight);

        // 2. Создание змейки
        const snakeGeometry = new THREE.BoxGeometry(1, 1, 1);
        const snakeMaterial = new THREE.MeshLambertMaterial({ color: 0x00ff00 });
        const snake = [];
        for (let i = 0; i < 5; i++) {
            const segment = new THREE.Mesh(snakeGeometry, snakeMaterial);
            segment.position.z = -i;
            snake.push(segment);
            scene.add(segment);
        }

        // 3. Создание еды
        const foodGeometry = new THREE.SphereGeometry(0.5, 32, 32);
        const foodMaterial = new THREE.MeshLambertMaterial({ color: 0xff0000 });
        const food = new THREE.Mesh(foodGeometry, foodMaterial);
        newFoodPosition();
        scene.add(food);

        function newFoodPosition() {
            food.position.set(Math.random() * 10 - 5, Math.random() * 10 - 5, 0);
        }

        // 4. Управление
        let direction = new THREE.Vector3(0, 0, -1);
        document.addEventListener('keydown', (event) => {
            switch (event.code) {
                case 'ArrowUp': direction.set(0, 1, 0); break;
                case 'ArrowDown': direction.set(0, -1, 0); break;
                case 'ArrowLeft': direction.set(-1, 0, 0); break;
                case 'ArrowRight': direction.set(1, 0, 0); break;
            }
        });

        // 5. Логика игры
        let score = 0;
        const scoreElement = document.createElement('div');
        scoreElement.style.position = 'absolute';
        scoreElement.style.top = '10px';
        scoreElement.style.left = '10px';
        scoreElement.style.color = 'white';
        scoreElement.textContent = 'Score: ' + score;
        document.body.appendChild(scoreElement);

        function update() {
            const head = snake[0];
            const newHead = new THREE.Mesh(snakeGeometry, snakeMaterial);
            newHead.position.copy(head.position).add(direction);
            snake.unshift(newHead);
            scene.add(newHead);

            if (head.position.distanceTo(food.position) < 0.75) {
                score++;
                scoreElement.textContent = 'Score: ' + score;
                newFoodPosition();
            } else {
                const tail = snake.pop();
                scene.remove(tail);
            }
        }

        // 6. Анимация
        camera.position.set(0, 5, 15);
        camera.lookAt(0, 0, 0);

        function animate() {
            requestAnimationFrame(animate);
            update();
            renderer.render(scene, camera);
        }

        animate();
    </script>
</body>
</html>
