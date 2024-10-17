// Select canvas
const canvas = document.getElementById("gameCanvas");
const ctx = canvas.getContext("2d");

// Canvas dimensions
canvas.width = 320;
canvas.height = 480;

// Bird variables
const bird = {
    x: 50,
    y: 150,
    width: 20,
    height: 20,
    gravity: 0.5,
    lift: -10,
    velocity: 0
};

// Pipe variables
const pipes = [];
const pipeWidth = 40;
const pipeGap = 100;
const pipeSpeed = 2;

// Game state
let isGameOver = false;
let score = 0;

// Add pipe at intervals
function addPipe() {
    let pipeHeight = Math.floor(Math.random() * 150) + 50;
    pipes.push({
        x: canvas.width,
        y: pipeHeight
    });
}

// Update bird and pipes
function update() {
    // Apply gravity
    bird.velocity += bird.gravity;
    bird.y += bird.velocity;

    // Check for collisions
    if (bird.y + bird.height >= canvas.height || bird.y <= 0) {
        isGameOver = true;
    }

    // Move pipes
    pipes.forEach((pipe, index) => {
        pipe.x -= pipeSpeed;

        // Remove pipe when off screen
        if (pipe.x + pipeWidth < 0) {
            pipes.splice(index, 1);
            score++;
        }

        // Check collision with pipes
        if (
            bird.x < pipe.x + pipeWidth &&
            bird.x + bird.width > pipe.x &&
            (bird.y < pipe.y || bird.y + bird.height > pipe.y + pipeGap)
        ) {
            isGameOver = true;
        }
    });

    // Add new pipes
    if (pipes.length === 0 || pipes[pipes.length - 1].x < canvas.width / 2) {
        addPipe();
    }
}

// Draw bird and pipes
function draw() {
    ctx.clearRect(0, 0, canvas.width, canvas.height);

    // Draw bird
    ctx.fillStyle = "yellow";
    ctx.fillRect(bird.x, bird.y, bird.width, bird.height);

    // Draw pipes
    pipes.forEach(pipe => {
        ctx.fillStyle = "green";
        ctx.fillRect(pipe.x, 0, pipeWidth, pipe.y);
        ctx.fillRect(pipe.x, pipe.y + pipeGap, pipeWidth, canvas.height - pipe.y - pipeGap);
    });

    // Draw score
    ctx.fillStyle = "black";
    ctx.font = "20px Arial";
    ctx.fillText(`Score: ${score}`, 10, 30);
}

// Bird jump on space key press
function jump() {
    bird.velocity = bird.lift;
}

// Restart game on click
function restart() {
    if (isGameOver) {
        bird.y = 150;
        bird.velocity = 0;
        pipes.length = 0;
        score = 0;
        isGameOver = false;
    }
}

// Game loop
function gameLoop() {
    if (!isGameOver) {
        update();
        draw();
        requestAnimationFrame(gameLoop);
    } else {
        ctx.fillStyle = "red";
        ctx.font = "30px Arial";
        ctx.fillText("Game Over", canvas.width / 4, canvas.height / 2);
        ctx.fillText(`Score: ${score}`, canvas.width / 3, canvas.height / 2 + 40);
        canvas.addEventListener("click", restart);
    }
}

// Event listeners
document.addEventListener("keydown", function(event) {
    if (event.code === "Space") jump();
});

// Start the game
gameLoop();
