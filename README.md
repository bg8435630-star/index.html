# index.html
This is the game of neorush which is very fun to play.
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Neon Rush</title>

<style>
* {
    box-sizing: border-box;
    margin: 0;
    padding: 0;
}

body {
    min-height: 100vh;
    font-family: Arial, Helvetica, sans-serif;
    background:
        radial-gradient(circle at 50% 20%, #24356b 0%, #10152d 35%, #050711 75%);
    color: white;
    overflow: hidden;
    user-select: none;
}

.game-wrapper {
    width: 100%;
    min-height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
    padding: 15px;
}

.game {
    position: relative;
    width: min(950px, 100%);
    height: min(650px, 90vh);
    min-height: 500px;
    overflow: hidden;
    border: 2px solid rgba(120, 170, 255, .4);
    border-radius: 24px;
    background: #090d1c;
    box-shadow:
        0 0 50px rgba(40, 100, 255, .25),
        inset 0 0 50px rgba(40, 100, 255, .05);
}

canvas {
    width: 100%;
    height: 100%;
    display: block;
}

.hud {
    position: absolute;
    top: 18px;
    left: 18px;
    right: 18px;
    display: flex;
    justify-content: space-between;
    align-items: center;
    z-index: 5;
    pointer-events: none;
}

.stats {
    display: flex;
    gap: 10px;
    flex-wrap: wrap;
}

.stat {
    background: rgba(5, 10, 30, .75);
    border: 1px solid rgba(130, 170, 255, .25);
    padding: 10px 15px;
    border-radius: 12px;
    backdrop-filter: blur(10px);
    font-weight: bold;
}

.stat span {
    color: #64d8ff;
}

.sound {
    pointer-events: auto;
    border: 1px solid rgba(130, 170, 255, .3);
    background: rgba(5, 10, 30, .75);
    color: white;
    padding: 10px 13px;
    border-radius: 12px;
    cursor: pointer;
    font-size: 17px;
}

.overlay {
    position: absolute;
    inset: 0;
    z-index: 10;
    display: flex;
    justify-content: center;
    align-items: center;
    background: rgba(3, 5, 15, .72);
    backdrop-filter: blur(7px);
    text-align: center;
    padding: 25px;
}

.panel {
    width: min(500px, 100%);
    padding: 40px 25px;
    border: 1px solid rgba(100, 200, 255, .25);
    border-radius: 24px;
    background: rgba(10, 16, 40, .9);
    box-shadow: 0 20px 60px rgba(0, 0, 0, .45);
}

.logo {
    font-size: clamp(45px, 8vw, 75px);
    font-weight: 900;
    letter-spacing: -4px;
    color: #70ddff;
    text-shadow:
        0 0 10px #00bfff,
        0 0 30px rgba(0, 191, 255, .7);
}

.subtitle {
    margin-top: 10px;
    color: #aab5d6;
    line-height: 1.6;
}

.play-btn {
    margin-top: 28px;
    padding: 15px 35px;
    border: 0;
    border-radius: 14px;
    background: linear-gradient(135deg, #19c8ff, #6764ff);
    color: white;
    font-size: 18px;
    font-weight: 800;
    cursor: pointer;
    box-shadow: 0 10px 30px rgba(70, 120, 255, .3);
    transition: .2s;
}

.play-btn:hover {
    transform: translateY(-2px) scale(1.03);
}

.instructions {
    margin-top: 20px;
    color: #8793b8;
    font-size: 14px;
}

.hidden {
    display: none;
}

.final-score {
    font-size: 26px;
    margin-top: 15px;
    color: #64d8ff;
    font-weight: bold;
}

.credit {
    position: absolute;
    bottom: 10px;
    left: 0;
    right: 0;
    text-align: center;
    color: rgba(255,255,255,.25);
    font-size: 11px;
    z-index: 4;
    pointer-events: none;
}

@media (max-width: 600px) {
    .game-wrapper {
        padding: 0;
    }

    .game {
        width: 100%;
        height: 100vh;
        min-height: 100vh;
        border-radius: 0;
        border: 0;
    }

    .hud {
        top: 10px;
        left: 10px;
        right: 10px;
    }

    .stat {
        padding: 8px 10px;
        font-size: 13px;
    }
}
</style>
</head>

<body>

<div class="game-wrapper">

    <div class="game">

        <canvas id="gameCanvas"></canvas>

        <div class="hud">
            <div class="stats">
                <div class="stat">⭐ <span id="score">0</span></div>
                <div class="stat">🪙 <span id="coins">0</span></div>
                <div class="stat">❤️ <span id="lives">3</span></div>
            </div>

            <button class="sound" id="soundBtn">🔊</button>
        </div>

        <div class="overlay" id="startScreen">
            <div class="panel">
                <div class="logo">NEON RUSH</div>
                <div class="subtitle">
                    Run. Jump. Collect. Survive.
                    <br>
                    How long can you last?
                </div>

                <button class="play-btn" id="startBtn">
                    PLAY NOW
                </button>

                <div class="instructions">
                    SPACE / ↑ to jump<br>
                    On mobile: tap the screen
                </div>
            </div>
        </div>

        <div class="overlay hidden" id="gameOverScreen">
            <div class="panel">
                <div class="logo" style="font-size:48px;">
                    GAME OVER
                </div>

                <div class="final-score">
                    Score: <span id="finalScore">0</span>
                </div>

                <div class="instructions">
                    Best Score: <span id="bestScore">0</span>
                </div>

                <button class="play-btn" id="restartBtn">
                    PLAY AGAIN
                </button>
            </div>
        </div>

        <div class="credit">
            NEON RUSH • HTML5 GAME
        </div>

    </div>

</div>

<script>
const canvas = document.getElementById("gameCanvas");
const ctx = canvas.getContext("2d");

const scoreText = document.getElementById("score");
const coinsText = document.getElementById("coins");
const livesText = document.getElementById("lives");

const startScreen = document.getElementById("startScreen");
const gameOverScreen = document.getElementById("gameOverScreen");

const startBtn = document.getElementById("startBtn");
const restartBtn = document.getElementById("restartBtn");
const soundBtn = document.getElementById("soundBtn");

const finalScore = document.getElementById("finalScore");
const bestScoreText = document.getElementById("bestScore");

let W = 900;
let H = 600;

function resizeCanvas() {
    const rect = canvas.getBoundingClientRect();

    const ratio = window.devicePixelRatio || 1;

    W = rect.width;
    H = rect.height;

    canvas.width = W * ratio;
    canvas.height = H * ratio;

    ctx.setTransform(ratio, 0, 0, ratio, 0, 0);
}

window.addEventListener("resize", resizeCanvas);
resizeCanvas();

let running = false;
let animationId;

let score = 0;
let coins = 0;
let lives = 3;

let speed = 6;
let spawnTimer = 0;
let coinTimer = 0;

let soundEnabled = true;

let bestScore = Number(localStorage.getItem("neonRushBest")) || 0;

let player;
let obstacles = [];
let coinObjects = [];
let particles = [];
let stars = [];

function createStars() {
    stars = [];

    for (let i = 0; i < 80; i++) {
        stars.push({
            x: Math.random() * W,
            y: Math.random() * H * .65,
            size: Math.random() * 2 + .5,
            speed: Math.random() * .5 + .2
        });
    }
}

createStars();

function resetGame() {

    score = 0;
    coins = 0;
    lives = 3;

    speed = 6;
    spawnTimer = 0;
    coinTimer = 0;

    obstacles = [];
    coinObjects = [];
    particles = [];

    player = {
        x: W * .18,
        y: groundY() - 65,
        width: 45,
        height: 65,
        velocityY: 0,
        jumping: false,
        color: "#63e6ff",
        invincible: 0
    };

    updateHUD();
}

function groundY() {
    return H * .82;
}

function updateHUD() {
    scoreText.textContent = Math.floor(score);
    coinsText.textContent = coins;
    livesText.textContent = lives;
}

function jump() {

    if (!running) return;

    if (!player.jumping) {

        player.velocityY = -15;
        player.jumping = true;

        beep(520, .08);
    }
}

function spawnObstacle() {

    const height = 35 + Math.random() * 45;
    const width = 25 + Math.random() * 35;

    obstacles.push({
        x: W + 30,
        y: groundY() - height,
        width,
        height,
        passed: false
    });
}

function spawnCoin() {

    coinObjects.push({
        x: W + 30,
        y: groundY() - 100 - Math.random() * 130,
        radius: 10,
        rotation: 0
    });
}

function createParticles(x, y, amount = 10) {

    for (let i = 0; i < amount; i++) {

        particles.push({
            x,
            y,
            vx: (Math.random() - .5) * 6,
            vy: (Math.random() - .5) * 6,
            life: 1,
            size: Math.random() * 4 + 2
        });
    }
}

function collision(a, b) {

    return (
        a.x < b.x + b.width &&
        a.x + a.width > b.x &&
        a.y < b.y + b.height &&
        a.y + a.height > b.y
    );
}

function coinCollision(c) {

    const closestX = Math.max(
        player.x,
        Math.min(c.x, player.x + player.width)
    );

    const closestY = Math.max(
        player.y,
        Math.min(c.y, player.y + player.height)
    );

    const dx = c.x - closestX;
    const dy = c.y - closestY;

    return dx * dx + dy * dy < c.radius * c.radius;
}

function hitObstacle(index) {

    if (player.invincible > 0) return;

    obstacles.splice(index, 1);

    lives--;

    player.invincible = 90;

    createParticles(
        player.x + player.width / 2,
        player.y + player.height / 2,
        20
    );

    beep(100, .18);

    if (lives <= 0) {
        endGame();
    }

    updateHUD();
}

function update() {

    if (!running) return;

    // Difficulty
    speed += 0.0015;

    // Score
    score += 0.03 * speed;

    // Player gravity
    player.velocityY += .7;
    player.y += player.velocityY;

    const floor = groundY() - player.height;

    if (player.y >= floor) {

        player.y = floor;
        player.velocityY = 0;
        player.jumping = false;
    }

    if (player.invincible > 0) {
        player.invincible--;
    }

    // Spawn obstacles
    spawnTimer--;

    if (spawnTimer <= 0) {

        spawnObstacle();

        spawnTimer =
            Math.max(45, 100 - speed * 4) +
            Math.random() * 70;
    }

    // Spawn coins
    coinTimer--;

    if (coinTimer <= 0) {

        spawnCoin();

        coinTimer = 90 + Math.random() * 100;
    }

    // Move obstacles
    for (let i = obstacles.length - 1; i >= 0; i--) {

        const o = obstacles[i];

        o.x -= speed;

        if (!o.passed && o.x + o.width < player.x) {

            o.passed = true;
            score += 10;
        }

        if (collision(player, o)) {
            hitObstacle(i);
        }

        else if (o.x + o.width < -50) {
            obstacles.splice(i, 1);
        }
    }

    // Move coins
    for (let i = coinObjects.length - 1; i >= 0; i--) {

        const c = coinObjects[i];

        c.x -= speed;
        c.rotation += .1;

        if (coinCollision(c)) {

            coins++;
            score += 25;

            createParticles(c.x, c.y, 12);

            beep(800, .06);

            coinObjects.splice(i, 1);
        }

        else if (c.x < -30) {
            coinObjects.splice(i, 1);
        }
    }

    // Particles
    for (let i = particles.length - 1; i >= 0; i--) {

        const p = particles[i];

        p.x += p.vx;
        p.y += p.vy;

        p.vy += .1;
        p.life -= .025;

        if (p.life <= 0) {
            particles.splice(i, 1);
        }
    }

    updateHUD();
}

function drawBackground() {

    const gradient = ctx.createLinearGradient(0, 0, 0, H);

    gradient.addColorStop(0, "#070a1c");
    gradient.addColorStop(.65, "#111a3b");
    gradient.addColorStop(1, "#07101c");

    ctx.fillStyle = gradient;
    ctx.fillRect(0, 0, W, H);

    // Moon glow
    const moonX = W * .78;
    const moonY = H * .22;

    const glow = ctx.createRadialGradient(
        moonX,
        moonY,
        10,
        moonX,
        moonY,
        120
    );

    glow.addColorStop(0, "rgba(120,220,255,.35)");
    glow.addColorStop(1, "rgba(120,220,255,0)");

    ctx.fillStyle = glow;
    ctx.fillRect(
        moonX - 120,
        moonY - 120,
        240,
        240
    );

    ctx.beginPath();
    ctx.arc(moonX, moonY, 35, 0, Math.PI * 2);

    ctx.fillStyle = "#d8f7ff";
    ctx.shadowBlur = 30;
    ctx.shadowColor = "#60dfff";
    ctx.fill();
    ctx.shadowBlur = 0;

    // Stars
    stars.forEach(s => {

        s.x -= s.speed;

        if (s.x < 0) {
            s.x = W;
        }

        ctx.beginPath();

        ctx.arc(
            s.x,
            s.y,
            s.size,
            0,
            Math.PI * 2
        );

        ctx.fillStyle = "rgba(210,240,255,.7)";
        ctx.fill();
    });

    // City skyline
    const buildingWidth = 55;

    for (
        let x = 0;
        x < W + buildingWidth;
        x += buildingWidth
    ) {

        const buildingHeight =
            40 + Math.abs(Math.sin(x * 2.3)) * 120;

        ctx.fillStyle = "#080d20";

        ctx.fillRect(
            x,
            groundY() - buildingHeight,
            buildingWidth - 8,
            buildingHeight
        );

        for (
            let y = groundY() - buildingHeight + 15;
            y < groundY() - 10;
            y += 18
        ) {

            if (Math.random() > .55) {

                ctx.fillStyle = "rgba(100,210,255,.35)";

                ctx.fillRect(
                    x + 10,
                    y,
                    7,
                    5
                );
            }
        }
    }

    // Ground
    ctx.fillStyle = "#050913";

    ctx.fillRect(
        0,
        groundY(),
        W,
        H - groundY()
    );

    // Neon road line
    ctx.strokeStyle = "#27c9ff";
    ctx.lineWidth = 3;
    ctx.shadowBlur = 15;
    ctx.shadowColor = "#27c9ff";

    ctx.beginPath();
    ctx.moveTo(0, groundY());
    ctx.lineTo(W, groundY());
    ctx.stroke();

    ctx.shadowBlur = 0;

    // Road lines
    const lineOffset = (Date.now() / 8 * speed) % 80;

    ctx.fillStyle = "rgba(80,200,255,.2)";

    for (
        let x = -80 + lineOffset;
        x < W;
        x += 80
    ) {

        ctx.fillRect(
            x,
            groundY() + 55,
            40,
            3
        );
    }
}

function drawPlayer() {

    if (
        player.invincible > 0 &&
        Math.floor(player.invincible / 6) % 2 === 0
    ) {
        return;
    }

    ctx.save();

    ctx.shadowBlur = 25;
    ctx.shadowColor = "#35ddff";

    // Body
    ctx.fillStyle = player.color;

    roundRect(
        ctx,
        player.x,
        player.y + 18,
        player.width,
        player.height - 18,
        10
    );

    // Head
    ctx.fillStyle = "#d9fbff";

    ctx.beginPath();

    ctx.arc(
        player.x + player.width / 2,
        player.y + 12,
        17,
        0,
        Math.PI * 2
    );

    ctx.fill();

    // Visor
    ctx.fillStyle = "#101c3c";

    roundRect(
        ctx,
        player.x + 7,
        player.y + 6,
        31,
        10,
        5
    );

    // Energy stripe
    ctx.fillStyle = "#ffffff";

    ctx.fillRect(
        player.x + 8,
        player.y + 32,
        5,
        22
    );

    ctx.restore();
}

function drawObstacles() {

    obstacles.forEach(o => {

        ctx.save();

        ctx.shadowBlur = 20;
        ctx.shadowColor = "#ff3864";

        const gradient = ctx.createLinearGradient(
            o.x,
            o.y,
            o.x + o.width,
            o.y + o.height
        );

        gradient.addColorStop(0, "#ff416c");
        gradient.addColorStop(1, "#8b1534");

        ctx.fillStyle = gradient;

        roundRect(
            ctx,
            o.x,
            o.y,
            o.width,
            o.height,
            7
        );

        ctx.restore();
    });
}

function drawCoins() {

    coinObjects.forEach(c => {

        ctx.save();

        ctx.translate(c.x, c.y);

        ctx.rotate(c.rotation);

        ctx.shadowBlur = 20;
        ctx.shadowColor = "#ffe44d";

        ctx.fillStyle = "#ffd83d";

        ctx.beginPath();

        ctx.arc(
            0,
            0,
            c.radius,
            0,
            Math.PI * 2
        );

        ctx.fill();

        ctx.fillStyle = "#fff4a0";

        ctx.beginPath();

        ctx.arc(
            -3,
            -3,
            3,
            0,
            Math.PI * 2
        );

        ctx.fill();

        ctx.restore();
    });
}

function drawParticles() {

    particles.forEach(p => {

        ctx.globalAlpha = p.life;

        ctx.fillStyle = "#6ee7ff";

        ctx.beginPath();

        ctx.arc(
            p.x,
            p.y,
            p.size,
            0,
            Math.PI * 2
        );

        ctx.fill();
    });

    ctx.globalAlpha = 1;
}

function roundRect(ctx, x, y, w, h, r) {

    ctx.beginPath();

    ctx.moveTo(x + r, y);

    ctx.arcTo(
        x + w,
        y,
        x + w,
        y + h,
        r
    );

    ctx.arcTo(
        x + w,
        y + h,
        x,
        y + h,
        r
    );

    ctx.arcTo(
        x,
        y + h,
        x,
        y,
        r
    );

    ctx.arcTo(
        x,
        y,
        x + w,
        y,
        r
    );

    ctx.closePath();
    ctx.fill();
}

function draw() {

    ctx.clearRect(0, 0, W, H);

    drawBackground();
    drawCoins();
    drawObstacles();
    drawPlayer();
    drawParticles();
}

function gameLoop() {

    update();
    draw();

    animationId = requestAnimationFrame(gameLoop);
}

function startGame() {

    resetGame();

    running = true;

    startScreen.classList.add("hidden");
    gameOverScreen.classList.add("hidden");

    cancelAnimationFrame(animationId);

    gameLoop();
}

function endGame() {

    running = false;

    cancelAnimationFrame(animationId);

    const final = Math.floor(score);

    if (final > bestScore) {

        bestScore = final;

        localStorage.setItem(
            "neonRushBest",
            bestScore
        );
    }

    finalScore.textContent = final;
    bestScoreText.textContent = bestScore;

    gameOverScreen.classList.remove("hidden");
}

let audioContext;

function beep(frequency, duration) {

    if (!soundEnabled) return;

    try {

        if (!audioContext) {
            audioContext =
                new (window.AudioContext ||
                window.webkitAudioContext)();
        }

        const oscillator =
            audioContext.createOscillator();

        const gain =
            audioContext.createGain();

        oscillator.frequency.value = frequency;
        oscillator.type = "square";

        gain.gain.setValueAtTime(
            .06,
            audioContext.currentTime
        );

        gain.gain.exponentialRampToValueAtTime(
            .001,
            audioContext.currentTime + duration
        );

        oscillator.connect(gain);
        gain.connect(audioContext.destination);

        oscillator.start();

        oscillator.stop(
            audioContext.currentTime + duration
        );

    } catch (e) {}
}

startBtn.addEventListener("click", startGame);
restartBtn.addEventListener("click", startGame);

soundBtn.addEventListener("click", () => {

    soundEnabled = !soundEnabled;

    soundBtn.textContent =
        soundEnabled ? "🔊" : "🔇";
});

document.addEventListener("keydown", e => {

    if (
        e.code === "Space" ||
        e.code === "ArrowUp"
    ) {

        e.preventDefault();

        if (!running) {
            startGame();
        } else {
            jump();
        }
    }
});

canvas.addEventListener(
    "touchstart",
    e => {

        e.preventDefault();

        if (!running) {
            startGame();
        } else {
            jump();
        }
    },
    { passive: false }
);

canvas.addEventListener(
    "mousedown",
    () => {

        if (running) {
            jump();
        }
    }
);

resetGame();
draw();

</script>

</body>
</html>
