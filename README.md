const canvas = document.getElementById('pong');
const ctx = canvas.getContext('2d');

const WIDTH = canvas.width;
const HEIGHT = canvas.height;

// Paddle settings
const PADDLE_WIDTH = 12;
const PADDLE_HEIGHT = 100;
const PADDLE_SPEED = 5; 

// Ball settings
const BALL_RADIUS = 10;
const BALL_SPEED = 5;

// Paddle objects
const leftPaddle = {
    x: 10,
    y: HEIGHT / 2 - PADDLE_HEIGHT / 2,
    width: PADDLE_WIDTH,
    height: PADDLE_HEIGHT
};

const rightPaddle = {
    x: WIDTH - PADDLE_WIDTH - 10,
    y: HEIGHT / 2 - PADDLE_HEIGHT / 2,
    width: PADDLE_WIDTH,
    height: PADDLE_HEIGHT
};

// Ball object
const ball = {
    x: WIDTH / 2,
    y: HEIGHT / 2,
    vx: BALL_SPEED * (Math.random() > 0.5 ? 1 : -1),
    vy: BALL_SPEED * (Math.random() * 2 - 1)
};

// Scores
let leftScore = 0;
let rightScore = 0;

// Mouse movement for left paddle
canvas.addEventListener('mousemove', function(e) {
    const rect = canvas.getBoundingClientRect();
    const mouseY = e.clientY - rect.top;
    leftPaddle.y = mouseY - leftPaddle.height / 2;
    // Clamp
    leftPaddle.y = Math.max(0, Math.min(HEIGHT - leftPaddle.height, leftPaddle.y));
});

// Draw everything
function draw() {
    // Clear
    ctx.clearRect(0, 0, WIDTH, HEIGHT);

    // Net
    ctx.strokeStyle = 'white';
    ctx.beginPath();
    ctx.setLineDash([8, 16]);
    ctx.moveTo(WIDTH / 2, 0);
    ctx.lineTo(WIDTH / 2, HEIGHT);
    ctx.stroke();
    ctx.setLineDash([]);

    // Left paddle
    ctx.fillStyle = '#0ff';
    ctx.fillRect(leftPaddle.x, leftPaddle.y, leftPaddle.width, leftPaddle.height);

    // Right paddle
    ctx.fillStyle = '#f00';
    ctx.fillRect(rightPaddle.x, rightPaddle.y, rightPaddle.width, rightPaddle.height);

    // Ball
    ctx.beginPath();
    ctx.arc(ball.x, ball.y, BALL_RADIUS, 0, Math.PI * 2);
    ctx.fillStyle = '#fff';
    ctx.fill();

    // Scores
    ctx.font = '32px Arial';
    ctx.fillStyle = '#fff';
    ctx.fillText(leftScore, WIDTH / 2 - 50, 50);
    ctx.fillText(rightScore, WIDTH / 2 + 20, 50);
}

// Ball movement
function updateBall() {
    ball.x += ball.vx;
    ball.y += ball.vy;

    // Top and bottom collision
    if (ball.y - BALL_RADIUS < 0) {
        ball.y = BALL_RADIUS;
        ball.vy *= -1;
    }
    if (ball.y + BALL_RADIUS > HEIGHT) {
        ball.y = HEIGHT - BALL_RADIUS;
        ball.vy *= -1;
    }

    // Left paddle collision
    if (
        ball.x - BALL_RADIUS < leftPaddle.x + leftPaddle.width &&
        ball.y > leftPaddle.y &&
        ball.y < leftPaddle.y + leftPaddle.height
    ) {
        ball.x = leftPaddle.x + leftPaddle.width + BALL_RADIUS;
        ball.vx *= -1;

        // Add some "spin" based on where paddle was hit
        const impact = (ball.y - (leftPaddle.y + leftPaddle.height / 2)) / (PADDLE_HEIGHT / 2);
        ball.vy += impact * 2;
    }

    // Right paddle collision
    if (
        ball.x + BALL_RADIUS > rightPaddle.x &&
        ball.y > rightPaddle.y &&
        ball.y < rightPaddle.y + rightPaddle.height
    ) {
        ball.x = rightPaddle.x - BALL_RADIUS;
        ball.vx *= -1;

        // Add spin
        const impact = (ball.y - (rightPaddle.y + rightPaddle.height / 2)) / (PADDLE_HEIGHT / 2);
        ball.vy += impact * 2;
    }

    // Left wall (right scores)
    if (ball.x - BALL_RADIUS < 0) {
        rightScore++;
        resetBall(-1);
    }
    // Right wall (left scores)
    if (ball.x + BALL_RADIUS > WIDTH) {
        leftScore++;
        resetBall(1);
    }
}

// AI for right paddle
function updateAIPaddle() {
    // Simple AI: move towards the ball's Y coordinate
    const paddleCenter = rightPaddle.y + rightPaddle.height / 2;
    if (ball.y < paddleCenter - 10) {
        rightPaddle.y -= PADDLE_SPEED;
    } else if (ball.y > paddleCenter + 10) {
        rightPaddle.y += PADDLE_SPEED;
    }
    // Clamp
    rightPaddle.y = Math.max(0, Math.min(HEIGHT - rightPaddle.height, rightPaddle.y));
}

function resetBall(direction = 1) {
    ball.x = WIDTH / 2;
    ball.y = HEIGHT / 2;
    ball.vx = BALL_SPEED * direction;
    ball.vy = BALL_SPEED * (Math.random() * 2 - 1);
}

// Game loop
function loop() {
    updateBall();
    updateAIPaddle();
    draw();
    requestAnimationFrame(loop);
}

loop();# Poke-Game
POke Game
