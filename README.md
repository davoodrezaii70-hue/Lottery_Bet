index.html
Converting Lottery Bet website to Android app.

<!DOCTYPE html>
<html lang="fa" dir="rtl">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>LOTTERY BET</title>
<style>
*{box-sizing:border-box}
body{margin:0;background:#101323;color:white;font-family:tahoma,Arial}
header{background:#1b2140;padding:22px;text-align:center;border-bottom:2px solid #8b5cf6}
h1{color:#b99aff;margin:0}
header p{color:#bbb}
.wallet{background:#222b49;margin:15px;padding:16px;border-radius:14px;text-align:center}
.wallet strong{color:#54e6a0;font-size:24px}
nav{display:grid;grid-template-columns:repeat(2,1fr);gap:9px;padding:12px}
button{border:0;border-radius:10px;padding:13px;background:#7650d9;color:white;font-size:15px;cursor:pointer}
button:active{opacity:.8}
button.secondary{background:#35405e}
main{padding:12px}
.game{display:none;background:#1b2140;padding:20px;border-radius:16px;text-align:center;margin-bottom:15px}
.game.active{display:block}
h2{color:#c3a8ff}
.multiplier{font-size:48px;color:#56e5a0;margin:20px}
input{padding:12px;border-radius:9px;border:1px solid #555;background:#101323;color:white;width:100%;margin:10px 0}
.result{padding:12px;color:#ffe17c;min-height:35px}
footer{text-align:center;color:#888;padding:20px;font-size:12px}
</style>
</head>
<body>

<header>
<h1>LOTTERY BET</h1>
<p>دنیای بازی و سرگرمی مجازی</p>
</header>

<div class="wallet">
موجودی کیف پول مجازی:
<br><br>
<strong id="balance">1000</strong>
<br>سکه
</div>

<nav>
<button onclick="showGame('crash')">🚀 بازی کراش</button>
<button onclick="showGame('explosion')">💥 بازی انفجار</button>
<button onclick="showGame('dice')">🎲 بازی تاس</button>
<button onclick="showGame('lottery')">🎟️ لاتاری</button>
<button onclick="showGame('account')">👤 حساب کاربری</button>
<button onclick="showGame('wallet')">🪙 کیف پول</button>
</nav>

<main>

<section class="game active" id="crash">
<h2>🚀 بازی کراش</h2>
<div class="multiplier" id="multi">1.00x</div>
<input id="crashBet" type="number" min="1" placeholder="مقدار سکه">
<button onclick="startCrash()">شروع بازی</button>
<button onclick="cashOut()" class="secondary">برداشت سکه</button>
<div class="result" id="crashResult">برای شروع آماده‌ای؟</div>
</section>

<section class="game" id="explosion">
<h2>💥 بازی انفجار</h2>
<p>ضریب را قبل از انفجار متوقف کن!</p>
<div class="multiplier" id="expMulti">1.00x</div>
<input id="expBet" type="number" min="1" placeholder="مقدار سکه">
<button onclick="startExplosion()">شروع انفجار</button>
<button onclick="stopExplosion()" class="secondary">برداشت</button>
<div class="result" id="expResult"></div>
</section>

<section class="game" id="dice">
<h2>🎲 بازی تاس</h2>
<p>یک عدد بین ۱ تا ۶ انتخاب کن.</p>
<input id="diceBet" type="number" min="1" placeholder="مقدار سکه">
<input id="diceNumber" type="number" min="1" max="6" placeholder="عدد انتخابی (۱ تا ۶)">
<button onclick="playDice()">پرتاب تاس</button>
<div class="multiplier" id="diceResult">🎲</div>
<div class="result" id="diceText"></div>
</section>

<section class="game" id="lottery">
<h2>🎟️ قرعه‌کشی لاتاری</h2>
<p>یک عدد از ۱ تا ۱۰ انتخاب کن.</p>
<input id="lotteryNumber" type="number" min="1" max="10" placeholder="عدد تو">
<button onclick="playLottery()">شرکت در قرعه‌کشی</button>
<div class="multiplier" id="lotteryResult">?</div>
<div class="result" id="lotteryText"></div>
</section>

<section class="game" id="account">
<h2>👤 حساب کاربری</h2>
<input id="username" placeholder="نام کاربری">
<button onclick="saveUser()">ثبت نام / ورود نمایشی</button>
<div class="result" id="userResult"></div>
</section>

<section class="game" id="wallet">
<h2>🪙 کیف پول مجازی</h2>
<div class="multiplier" id="walletBalance">1000</div>
<p>موجودی سکه مجازی شما</p>
<button onclick="addCoins()">دریافت ۱۰۰ سکه آزمایشی</button>
<div class="result">این سکه‌ها فقط برای بازی آزمایشی هستند.</div>
</section>

</main>

<footer>
LOTTERY BET © 2026
<br>نسخه آزمایشی سرگرمی با اعتبار مجازی
</footer>

<script>
let balance = Number(localStorage.getItem("lb_balance")) || 1000;
let running = false;
let crashTimer = null;
let expTimer = null;
let multiplier = 1;
let expMultiplier = 1;
let crashStake = 0;
let expStake = 0;
let username = localStorage.getItem("lb_user") || "";

function updateBalance(){
 document.getElementById("balance").textContent=balance;
 document.getElementById("walletBalance").textContent=balance;
 localStorage.setItem("lb_balance",balance);
}

function showGame(id){
 document.querySelectorAll(".game").forEach(g=>g.classList.remove("active"));
 document.getElementById(id).classList.add("active");
}

function getBet(id){
 let amount=Number(document.getElementById(id).value);
 if(!Number.isInteger(amount)||amount<1||amount>balance){
  alert("مقدار سکه معتبر نیست!");
  return 0;
 }
 return amount;
}

function startCrash(){
 if(running)return;
 crashStake=getBet("crashBet");
 if(!crashStake)return;
 balance-=crashStake;
 updateBalance();
 running=true;
 multiplier=1;
 let crashPoint=1.2+Math.random()*5;
 document.getElementById("crashResult").textContent="بازی شروع شد!";
 crashTimer=setInterval(()=>{
  multiplier+=0.03;
  document.getElementById("multi").textContent=multiplier.toFixed(2)+"x";
  if(multiplier>=crashPoint){
   clearInterval(crashTimer);
   running=false;
   crashStake=0;
   document.getElementById("crashResult").textContent="💥 کراش شد! این دور باختی.";
  }
 },100);
}

function cashOut(){
 if(!running)return;
 clearInterval(crashTimer);
 running=false;
 let prize=Math.floor(crashStake*multiplier);
 balance+=prize;
 crashStake=0;
 updateBalance();
 document.getElementById("crashResult").textContent="برداشت موفق! "+prize+" سکه دریافت کردی.";
}

function startExplosion(){
 if(expTimer)return;
 expStake=getBet("expBet");
 if(!expStake)return;
 balance-=expStake;
 updateBalance();
 expMultiplier=1;
 let point=1.2+Math.random()*5;
 document.getElementById("expResult").textContent="در حال افزایش ضریب...";
 expTimer=setInterval(()=>{
  expMultiplier+=0.04;
  document.getElementById("expMulti").textContent=expMultiplier.toFixed(2)+"x";
  if(expMultiplier>=point){
   clearInterval(expTimer);
   expTimer=null;
   expStake=0;
   document.getElementById("expResult").textContent="💥 انفجار! این دور باختی.";
  }
 },120);
}

function stopExplosion(){
 if(!expTimer)return;
 clearInterval(expTimer);
 expTimer=null;
 let prize=Math.floor(expStake*expMultiplier);
 balance+=prize;
 expStake=0;
 updateBalance();
 document.getElementById("expResult").textContent="برداشت کردی: "+prize+" سکه";
}

function playDice(){
 let bet=getBet("diceBet");
 let chosen=Number(document.getElementById("diceNumber").value);
 if(!bet)return;
 if(!Number.isInteger(chosen)||chosen<1||chosen>6){
  alert("عدد باید بین ۱ تا ۶ باشد.");
  return;
 }
 balance-=bet;
 let result=Math.floor(Math.random()*6)+1;
 document.getElementById("diceResult").textContent=result+" 🎲";
 if(result===chosen){
  let prize=bet*5;
  balance+=prize;
  document.getElementById("diceText").textContent="آفرین! "+prize+" سکه بردی.";
 }else{
  document.getElementById("diceText").textContent="این بار برنده نشدی.";
 }
 updateBalance();
}

function playLottery(){
 let chosen=Number(document.getElementById("lotteryNumber").value);
 if(!Number.isInteger(chosen)||chosen<1||chosen>10){
  alert("عدد باید بین ۱ تا ۱۰ باشد.");
  return;
 }
 let bet=10;
 if(balance<bet){
  alert("سکه کافی نداری.");
  return;
 }
 balance-=bet;
 let result=Math.floor(Math.random()*10)+1;
 document.getElementById("lotteryResult").textContent=result;
 if(chosen===result){
  balance+=80;
  document.getElementById("lotteryText").textContent="🎉 برنده شدی! ۸۰ سکه گرفتی.";
 }else{
  document.getElementById("lotteryText").textContent="این بار برنده نشدی. دوباره امتحان کن.";
 }
 updateBalance();
}

function addCoins(){
 balance+=100;
 updateBalance();
}

function saveUser(){
 let name=document.getElementById("username").value.trim();
 if(!name){
  alert("نام کاربری را وارد کن.");
  return;
 }
 username=name;
 localStorage.setItem("lb_user",name);
 document.getElementById("userResult").textContent="خوش آمدی "+name+" عزیز!";
}

if(username){
 document.getElementById("username").value=username;
 document.getElementById("userResult").textContent="خوش آمدی "+username;
}

updateBalance();
</script>
</body>
</html>
