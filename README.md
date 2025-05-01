# sigma-ping-ligma
funny and nice
onst canvas = document.getElementById("pongCanvas");
const ctx = canvas.getContext("2d");

const ball = {
  x: canvas.width / 2,
  y: canvas.height / 2,
  radius: 10,
  velocityX: 5,
  velocityY: 5,
  speed: 5,
  color: "#fff"
};

const paddleWidth = 10, paddleHeight = 100;
const player1 = {
  x: 0,
  y: canvas.height / 2 - paddleHeight / 2,
  width: paddleWidth,
  height: paddleHeight,
  color: "#fff",
  score: 0,
  upPressed: false,
  downPressed: false
};

const player2 = {
  x: canvas.width - paddleWidth,
  y: canvas.height / 2 - paddleHeight / 2,
  width: paddleWidth,
  height: paddleHeight,
  color: "#fff",
  score: 0,
  upPressed: false,
  downPressed: false
};

function drawRect(x, y, w, h, color) {
  ctx.fillStyle = color;
  ctx.fillRect(x, y, w, h);
}

function drawCircle(x, y, r, color) {
  ctx.fillStyle = color;
  ctx.beginPath();
  ctx.arc(x, y, r, 0, Math.PI * 2, false);
  ctx.closePath();
  ctx.fill();
}

function drawText(text, x, y) {
  ctx.fillStyle = "#fff";
  ctx.font = "32px sans-serif";
  ctx.fillText(text, x, y);
}

function collision(b, p) {
  b.top = b.y - b.radius;
  b.bottom = b.y + b.radius;
  b.left = b.x - b.radius;
  b.right = b.x + b.radius;

  p.top = p.y;
  p.bottom = p.y + p.height;
  p.left = p.x;
  p.right = p.x + p.width;

  return b.right > p.left && b.bottom > p.top && b.left < p.right && b.top < p.bottom;
}

function resetBall() {
  ball.x = canvas.width / 2;
  ball.y = canvas.height / 2;
  ball.velocityX = -ball.velocityX;
  ball.speed = 5;
}

function update() {
  // Paddle Bewegung
  if (player1.upPressed && player1.y > 0) player1.y -= 7;
  if (player1.downPressed && player1.y < canvas.height - player1.height) player1.y += 7;
  if (player2.upPressed && player2.y > 0) player2.y -= 7;
  if (player2.downPressed && player2.y < canvas.height - player2.height) player2.y += 7;

  // Ball Bewegung
  ball.x += ball.velocityX;
  ball.y += ball.velocityY;

  // Wände
  if (ball.y + ball.radius > canvas.height || ball.y - ball.radius < 0) {
    ball.velocityY = -ball.velocityY;
  }

  // Punkte
  if (ball.x - ball.radius < 0) {
    player2.score++;
    resetBall();
  } else if (ball.x + ball.radius > canvas.width) {
    player1.score++;
    resetBall();
  }

  // Kollision mit Spieler
  let paddle = (ball.x < canvas.width / 2) ? player1 : player2;

  if (collision(ball, paddle)) {
    let collidePoint = (ball.y - (paddle.y + paddle.height / 2));
    collidePoint = collidePoint / (paddle.height / 2);
    let angleRad = collidePoint * (Math.PI / 4);
    let direction = (ball.x < canvas.width / 2) ? 1 : -1;

    ball.velocityX = direction * ball.speed * Math.cos(angleRad);
    ball.velocityY = ball.speed * Math.sin(angleRad);
    ball.speed += 0.3;
  }
}

function render() {
  drawRect(0, 0, canvas.width, canvas.height, "#000");
  drawText(player1.score, canvas.width / 4, 50);
  drawText(player2.score, 3 * canvas.width / 4, 50);
  drawRect(player1.x, player1.y, player1.width, player1.height, player1.color);
  drawRect(player2.x, player2.y, player2.width, player2.height, player2.color);
  drawCircle(ball.x, ball.y, ball.radius, ball.color);
}

function game() {
  update();
  render();
}

setInterval(game, 1000 / 60);

// Tasteneingaben
document.addEventListener("keydown", function(e) {
  switch (e.key) {
    case "w": player1.upPressed = true; break;
    case "s": player1.downPressed = true; break;
    case "ArrowUp": player2.upPressed = true; break;
    case "ArrowDown": player2.downPressed = true; break;
  }
});

document.addEventListener("keyup", function(e) {
  switch (e.key) {
    case "w": player1.upPressed = false; break;
    case "s": player1.downPressed = false; break;
    case "ArrowUp": player2.upPressed = false; break;
    case "ArrowDown": player2.downPressed = false; break;
  }
});
