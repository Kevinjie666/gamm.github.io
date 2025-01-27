<!DOCTYPE html>
<html lang="zh">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>打败儿子们</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            text-align: center;
            background-color: #f0f0f0;
        }
        canvas {
            background-color: #ffffff;
            display: block;
            margin: 0 auto;
        }
        #gameOver {
            display: none;
            font-size: 24px;
            color: green;
            padding: 20px;
        }
    </style>
</head>
<body>
    <h1>打败儿子们</h1>
    <canvas id="gameCanvas" width="800" height="600"></canvas>
    <div id="gameOver">恭喜你成功打败了儿子们！</div>

    <script>
        const canvas = document.getElementById("gameCanvas");
        const ctx = canvas.getContext("2d");

        const keys = {
            w: false,
            s: false,
            a: false,
            d: false,
            space: false
        };

        const player = {
            x: 100,
            y: canvas.height / 2,
            width: 50,
            height: 50,
            color: 'black',
            speed: 5
        };

        const enemies = [
            {x: 300, y: 100, width: 50, height: 50, label: "曹博雄", hit: false},
            {x: 400, y: 200, width: 50, height: 50, label: "李奕渊", hit: false},
            {x: 500, y: 300, width: 50, height: 50, label: "杨凯尧", hit: false}
        ];

        const projectiles = [];

        // 监听键盘事件
        document.addEventListener('keydown', (e) => {
            if (e.key === "w") keys.w = true;
            if (e.key === "s") keys.s = true;
            if (e.key === "a") keys.a = true;
            if (e.key === "d") keys.d = true;
            if (e.key === " ") keys.space = true;
        });

        document.addEventListener('keyup', (e) => {
            if (e.key === "w") keys.w = false;
            if (e.key === "s") keys.s = false;
            if (e.key === "a") keys.a = false;
            if (e.key === "d") keys.d = false;
            if (e.key === " ") keys.space = false;
        });

        // 绘制方块和发射小球
        function drawPlayer() {
            ctx.fillStyle = player.color;
            ctx.fillRect(player.x, player.y, player.width, player.height);
        }

        function drawEnemies() {
            enemies.forEach(enemy => {
                if (!enemy.hit) {
                    ctx.fillStyle = 'red';
                    ctx.fillRect(enemy.x, enemy.y, enemy.width, enemy.height);
                    ctx.fillStyle = 'white';
                    ctx.font = '12px Arial';
                    ctx.fillText(enemy.label, enemy.x + 5, enemy.y + 25);
                }
            });
        }

        function drawProjectiles() {
            projectiles.forEach((proj, index) => {
                ctx.fillStyle = 'blue';
                ctx.beginPath();
                ctx.arc(proj.x, proj.y, 5, 0, Math.PI * 2);
                ctx.fill();
                proj.x += proj.speed;

                // 检查小球是否击中敌人
                enemies.forEach(enemy => {
                    if (!enemy.hit && proj.x > enemy.x && proj.x < enemy.x + enemy.width && proj.y > enemy.y && proj.y < enemy.y + enemy.height) {
                        enemy.hit = true;
                        projectiles.splice(index, 1);  // 删除击中的小球
                    }
                });
            });
        }

        function handlePlayerMovement() {
            if (keys.w && player.y > 0) player.y -= player.speed;
            if (keys.s && player.y < canvas.height - player.height) player.y += player.speed;
            if (keys.a && player.x > 0) player.x -= player.speed;
            if (keys.d && player.x < canvas.width - player.width) player.x += player.speed;
        }

        function handleProjectile() {
            if (keys.space) {
                projectiles.push({
                    x: player.x + player.width,
                    y: player.y + player.height / 2,
                    speed: 10
                });
            }
        }

        function checkGameOver() {
            if (enemies.every(enemy => enemy.hit)) {
                document.getElementById("gameOver").style.display = "block";
                cancelAnimationFrame(animationFrameId);
            }
        }

        function gameLoop() {
            ctx.clearRect(0, 0, canvas.width, canvas.height);

            handlePlayerMovement();
            handleProjectile();
            drawPlayer();
            drawEnemies();
            drawProjectiles();
            checkGameOver();

            requestAnimationFrame(gameLoop);
        }

        let animationFrameId = requestAnimationFrame(gameLoop);
    </script>
</body>
</html>
