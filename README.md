# star-catcher-game
My first mobile game - Star Catcher.
<!DOCTYPE html>
<html>
<head>
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Space Shooting Game</title>

<style>
body {
  margin: 0;
  background: #05051b;
  color: white;
  text-align: center;
  font-family: Arial;
  overflow: hidden;
}

h2 { color: cyan; }

canvas {
  background: #080820;
  border: 2px solid cyan;
  display: block;
  margin: auto;
}

button {
  padding: 15px 25px;
  margin: 8px;
  border: none;
  border-radius: 12px;
  background: #00c8ff;
  font-size: 20px;
  font-weight: bold;
}
</style>
</head>

<body>

<h2>🚀 SPACE SHOOTER</h2>
<p>Score: <span id="score">0</span>
 | Lives: <span id="lives">3</span></p>

<canvas id="game" width="350" height="450"></canvas>

<div>
<button id="left">⬅️</button>
<button id="fire">🔥 FIRE</button>
<button id="right">➡️</button>
</div>

<button onclick="restart()">🔄 RESTART</button>

<script>
const canvas = document.getElementById("game");
const ctx = canvas.getContext("2d");

let player, enemies, bullets, score, lives, gameOver;
let keys = {};

function restart() {
  player = {x:160, y:390, w:35, h:35};
  enemies = [];
  bullets = [];
  score = 0;
  lives = 3;
  gameOver = false;
  document.getElementById("score").innerText = score;
  document.getElementById("lives").innerText = lives;
}

function shoot() {
  if (!gameOver) {
    bullets.push({
      x: player.x + 16,
      y: player.y,
      w: 5,
      h: 12
    });
  }
}

function enemy() {
  if (!gameOver) {
    enemies.push({
      x: Math.random()*310,
      y: -30,
      size: 30
    });
  }
}

setInterval(enemy, 1000);
setInterval(shoot, 350);

function moveButton(id, direction) {
  let btn = document.getElementById(id);
  btn.addEventListener("pointerdown", e => {
    e.preventDefault();
    keys[direction] = true;
  });
  btn.addEventListener("pointerup", () => keys[direction] = false);
  btn.addEventListener("pointerleave", () => keys[direction] = false);
}

moveButton("left", "left");
moveButton("right", "right");

document.getElementById("fire").addEventListener("pointerdown", shoot);

document.addEventListener("keydown", e => {
  if (e.key === "ArrowLeft") keys.left = true;
  if (e.key === "ArrowRight") keys.right = true;
  if (e.code === "Space") shoot();
});

document.addEventListener("keyup", e => {
  if (e.key === "ArrowLeft") keys.left = false;
  if (e.key === "ArrowRight") keys.right = false;
});

function draw() {
  ctx.clearRect(0,0,350,450);

  if (gameOver) {
    ctx.fillStyle = "red";
    ctx.font = "35px Arial";
    ctx.fillText("GAME OVER", 65, 210);
    ctx.font = "18px Arial";
    ctx.fillStyle = "white";
    ctx.fillText("Press RESTART", 105, 245);
    return;
  }

  if (keys.left) player.x -= 5;
  if (keys.right) player.x += 5;

  player.x = Math.max(0, Math.min(315, player.x));

  // Spaceship
  ctx.font = "32px Arial";
  ctx.fillText("🚀", player.x, player.y+30);

  // Bullets
  bullets.forEach((b, i) => {
    b.y -= 7;
    ctx.fillStyle = "yellow";
    ctx.fillRect(b.x,b.y,b.w,b.h);
    if (b.y < 0) bullets.splice(i,1);
  });

  // Enemies
  enemies.forEach((e, i) => {
    e.y += 2;
    ctx.font = "27px Arial";
    ctx.fillText("👾",e.x,e.y+25);

    if (e.y > 450) {
      enemies.splice(i,1);
      lives--;
    }

    bullets.forEach((b,j) => {
      if (b.x < e.x+30 && b.x+5 > e.x &&
          b.y < e.y+30 && b.y+12 > e.y) {
        enemies.splice(i,1);
        bullets.splice(j,1);
        score += 10;
      }
    });

    if (player.x < e.x+30 &&
        player.x+35 > e.x &&
        player.y < e.y+30 &&
        player.y+35 > e.y) {
      enemies.splice(i,1);
      lives--;
    }
  });

  document.getElementById("score").innerText = score;
  document.getElementById("lives").innerText = lives;

  if (lives <= 0) gameOver = true;

  requestAnimationFrame(draw);
}

restart();
draw();
</script>

</body>
</html>
