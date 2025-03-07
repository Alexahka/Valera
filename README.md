<!DOCTYPE html>
<html>
<head>
    <title>Седобородый Сэм</title>
    <style>
        body { margin: 0; overflow: hidden; }
        canvas { background: #87CEEB; display: block; }
    </style>
</head>
<body>
<canvas id="game"></canvas>
<script>
const canvas = document.getElementById('game');
const ctx = canvas.getContext('2d');
canvas.width = 800;
canvas.height = 400;

// Персонаж
const player = {
    x: 50,
    y: 200,
    width: 40,
    height: 60,
    speed: 4,
    vy: 0,
    gravity: 0.5,
    jumpPower: -10,
    isOnGround: false,
    beardLength: 1,
    sprite: new Image()
};

player.sprite.src = 'https://i.imgur.com/2qX6X6O.png'; // Заглушка персонажа

// Бонусы
let bonuses = [
    {x: 200, y: 250, size: 20},
    {x: 400, y: 150, size: 20},
    {x: 600, y: 200, size: 20}
];

// Платформы
const platforms = [
    {x: 0, y: 350, width: 800, height: 20},
    {x: 200, y: 250, width: 100, height: 20},
    {x: 500, y: 180, width: 150, height: 20}
];

// Управление
const keys = { ArrowLeft: false, ArrowRight: false, Space: false };

document.addEventListener('keydown', (e) => keys[e.code] = true);
document.addEventListener('keyup', (e) => keys[e.code] = false);

function update() {
    // Движение влево и вправо
    if (keys.ArrowLeft) player.x -= player.speed;
    if (keys.ArrowRight) player.x += player.speed;

    // Гравитация
    player.vy += player.gravity;
    player.y += player.vy;
    
    player.isOnGround = false;

    // Проверка столкновений с платформами
    platforms.forEach(platform => {
        if (player.x < platform.x + platform.width &&
            player.x + player.width > platform.x &&
            player.y + player.height > platform.y &&
            player.y + player.height < platform.y + platform.height + player.vy) {
            
            player.y = platform.y - player.height;
            player.vy = 0;
            player.isOnGround = true;
        }
    });

    // Прыжок
    if (keys.Space && player.isOnGround) {
        player.vy = player.jumpPower;
    }

    // Ограничение выхода за границы экрана
    if (player.x < 0) player.x = 0;
    if (player.x + player.width > canvas.width) player.x = canvas.width - player.width;
    if (player.y + player.height > canvas.height) player.y = canvas.height - player.height;

    // Сбор бонусов
    bonuses = bonuses.filter(bonus => {
        if (player.x < bonus.x + bonus.size &&
            player.x + player.width > bonus.x &&
            player.y < bonus.y + bonus.size &&
            player.y + player.height > bonus.y) {
            
            player.beardLength = Math.min(player.beardLength + 1, 5);
            return false; // Удаляем собранный бонус
        }
        return true;
    });
}

function draw() {
    ctx.clearRect(0, 0, canvas.width, canvas.height);

    // Рисуем платформы
    ctx.fillStyle = '#8B4513';
    platforms.forEach(p => ctx.fillRect(p.x, p.y, p.width, p.height));

    // Рисуем бонусы
    ctx.fillStyle = '#FFD700';
    bonuses.forEach(b => ctx.fillRect(b.x, b.y, b.size, b.size));

    // Рисуем персонажа
    ctx.drawImage(player.sprite, player.x, player.y, player.width, player.height);

    // Рисуем бороду
    ctx.fillStyle = '#FFFFFF';
    ctx.beginPath();
    ctx.moveTo(player.x + 20, player.y + 50);
    ctx.lineTo(player.x + 20 - player.beardLength * 10, player.y + 50 + player.beardLength * 15);
    ctx.lineTo(player.x + 20 + player.beardLength * 10, player.y + 50 + player.beardLength * 15);
    ctx.fill();
}

function gameLoop() {
    update();
    draw();
    requestAnimationFrame(gameLoop);
}

player.sprite.onload = gameLoop;
</script>
</body>
</html>
