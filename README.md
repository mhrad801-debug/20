عالی — می‌خوام چند بازی کامل و کم‌حجمِ قابل‌کپی/قابل‌اجرا در مرورگر برات بذارم که هیچ دانش برنامه‌نویسی لازم نداره: فقط متن رو کپی کن داخل یک فایل با پسوند .html و بازش کن با مرورگر (یا آپلود کن تو سایت‌هایی مثل Itch.io / GitHub Pages / CodePen). هر بازی همراه با توضیحِ کنترل‌ها و روش اجراست.

پیشنهاد می‌کنم از بازی ساده شروع کنی (۱)، بعد سراغ Snake (۲) و بعد شوتِری (۳) — همه با HTML5 Canvas و جاوااسکریپت خودِ مرورگر، بدون نیاز به نصب چیزی.


---

۱) بازی ساده — «جمع‌کن میوه» (Catch the Fruit)

فایل جدید بساز (مثلاً catch.html) و کل متن زیر را در آن بذار و ذخیره کن. با فلش‌ها یا ماوس سبد رو حرکت بده، میوه‌ها می‌افتن، نمره می‌گیری.

<!doctype html>
<html lang="fa">
<head>
<meta charset="utf-8">
<title>جمع‌کن میوه</title>
<style>
  body { margin:0; background:#222; color:#fff; font-family: Arial, sans-serif; display:flex;align-items:center;justify-content:center;height:100vh; }
  canvas { background: linear-gradient(#87ceeb,#bfe9ff); border:4px solid #444; display:block; }
  #ui { position: absolute; top:10px; left:10px; }
</style>
</head>
<body>
<canvas id="game" width="600" height="500"></canvas>
<script>
const cvs = document.getElementById('game');
const ctx = cvs.getContext('2d');

let score = 0;
let lives = 3;
let gameOver = false;

const basket = { x: 280, y: 440, w: 80, h: 30, speed: 6 };
const fruits = [];
const fruitTypes = ['🍎','🍊','🍌','🍇'];

let keys = {};
document.addEventListener('keydown', e => keys[e.key] = true);
document.addEventListener('keyup', e => keys[e.key] = false);
cvs.addEventListener('mousemove', e => {
  const r = cvs.getBoundingClientRect();
  basket.x = e.clientX - r.left - basket.w/2;
});

function spawnFruit(){
  const x = Math.random() * (cvs.width - 30) + 15;
  const type = fruitTypes[Math.floor(Math.random()*fruitTypes.length)];
  fruits.push({ x, y: -20, vy: 2 + Math.random()*2, type });
}
setInterval(spawnFruit, 900);

function update(){
  if(gameOver) return;
  // keyboard controls
  if(keys['ArrowLeft'] || keys['a']) basket.x -= basket.speed;
  if(keys['ArrowRight'] || keys['d']) basket.x += basket.speed;
  basket.x = Math.max(0, Math.min(cvs.width - basket.w, basket.x));

  for(let i = fruits.length-1; i>=0; i--){
    const f = fruits[i];
    f.y += f.vy;
    // catch?
    if(f.y + 18 >= basket.y && f.x > basket.x - 10 && f.x < basket.x + basket.w + 10){
      score += 10;
      fruits.splice(i,1);
      continue;
    }
    // missed?
    if(f.y > cvs.height + 20){
      fruits.splice(i,1);
      lives--;
      if(lives <= 0){ gameOver = true; }
    }
  }
}

function draw(){
  // background
  ctx.clearRect(0,0,cvs.width,cvs.height);
  // ground
  ctx.fillStyle = '#2e8b57';
  ctx.fillRect(0, 470, cvs.width, 30);

  // basket
  ctx.fillStyle = '#8b4513';
  ctx.fillRect(basket.x, basket.y, basket.w, basket.h);
  ctx.fillStyle = '#fff';
  ctx.font = '18px sans-serif';
  ctx.fillText('سبد', basket.x + 20, basket.y + 20);

  // fruits
  ctx.font = '28px sans-serif';
  for(const f of fruits){
    ctx.fillText(f.type, f.x, f.y);
  }

  // UI
  ctx.fillStyle = '#000000cc';
  ctx.fillRect(10,10,180,60);
  ctx.fillStyle = '#fff';
  ctx.font = '16px sans-serif';
  ctx.fillText('نمره: ' + score, 20, 32);
  ctx.fillText('جان: ' + lives, 20, 54);

  if(gameOver){
    ctx.fillStyle = 'rgba(0,0,0,0.6)';
    ctx.fillRect(0,0,cvs.width,cvs.height);
    ctx.fillStyle = '#fff';
    ctx.font = '36px sans-serif';
    ctx.textAlign = 'center';
    ctx.fillText('اتمام بازی', cvs.width/2, cvs.height/2 - 10);
    ctx.font = '20px sans-serif';
    ctx.fillText('برای شروع دوباره کلیک کن', cvs.width/2, cvs.height/2 + 30);
  }
}

cvs.addEventListener('click', () => {
  if(gameOver){
    score = 0; lives = 3; fruits.length = 0; gameOver = false;
  }
});

function loop(){
  update();
  draw();
  requestAnimationFrame(loop);
}
loop();
</script>
</body>
</html>



