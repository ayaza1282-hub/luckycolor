index.html
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>Lucky Color</title>
<style>
*{box-sizing:border-box}
body{margin:0;background:#10182b;color:white;font-family:Arial,sans-serif}
header{background:#172541;padding:18px;text-align:center}
h1{color:#ffcf45;margin:0}
.container{max-width:480px;margin:auto;padding:14px}
.wallet{background:linear-gradient(120deg,#e5a72b,#ffcf58);color:#241900;padding:18px;border-radius:14px}
.wallet h2{font-size:28px;margin:8px 0}
button{border:0;border-radius:9px;padding:13px;font-weight:bold;cursor:pointer}
.wallet button{background:#172541;color:white}
.panel{background:#1b2944;padding:15px;border-radius:13px;margin-top:14px}
.timer{text-align:center;font-size:35px;color:#ffce46;font-weight:bold;margin:12px}
.period{text-align:center;color:#b9c9e5}
.colors,.numbers,.sizes{display:grid;gap:9px;margin-top:12px}
.colors{grid-template-columns:repeat(3,1fr)}
.numbers{grid-template-columns:repeat(5,1fr)}
.sizes{grid-template-columns:repeat(2,1fr)}
.pick{color:white;min-height:45px;font-size:15px}
.red{background:#e84452}
.green{background:#16a66a}
.violet{background:#9149d9}
.number{background:#263a60}
.big{background:#e69c29;color:#241900}
.small{background:#2879d7}
.selected{outline:3px solid white}
.play{width:100%;background:#ffca43;color:#241900;margin-top:16px;font-size:17px}
.history{width:100%;border-collapse:collapse;margin-top:12px}
th,td{padding:11px;text-align:center;border-bottom:1px solid #34435f}
.muted{color:#aebbd1;font-size:13px}
#message{text-align:center;color:#ffcf45;margin-top:13px}
</style>
</head>
<body>

<header>
<h1>LUCKY COLOR</h1>
<p>Play • Enjoy • Demo Coins</p>
</header>

<div class="container">

<div class="wallet">
<div>Demo Wallet Balance</div>
<h2><span id="balance">1000</span> Coins</h2>
<button onclick="addCoins()">+ Add 50 Demo Coins</button>
</div>

<div class="panel">
<h3>WINGO 30 SEC</h3>
<div class="period">Period: <span id="period">100001</span></div>
<div class="timer" id="timer">30</div>
<div class="muted" style="text-align:center">Next result in</div>
</div>

<div class="panel">
<h3>Select Color</h3>
<div class="colors">
<button class="pick red" onclick="choose('Red',this)">RED</button>
<button class="pick green" onclick="choose('Green',this)">GREEN</button>
<button class="pick violet" onclick="choose('Violet',this)">VIOLET</button>
</div>

<h3>Select Number</h3>
<div class="numbers">
<button class="pick number" onclick="choose('0',this)">0</button>
<button class="pick number" onclick="choose('1',this)">1</button>
<button class="pick number" onclick="choose('2',this)">2</button>
<button class="pick number" onclick="choose('3',this)">3</button>
<button class="pick number" onclick="choose('4',this)">4</button>
<button class="pick number" onclick="choose('5',this)">5</button>
<button class="pick number" onclick="choose('6',this)">6</button>
<button class="pick number" onclick="choose('7',this)">7</button>
<button class="pick number" onclick="choose('8',this)">8</button>
<button class="pick number" onclick="choose('9',this)">9</button>
</div>

<h3>Big / Small</h3>
<div class="sizes">
<button class="pick big" onclick="choose('Big',this)">BIG (5-9)</button>
<button class="pick small" onclick="choose('Small',this)">SMALL (0-4)</button>
</div>

<button class="play" onclick="play()">PLAY DEMO</button>
<div id="message">Choose your option</div>
</div>

<div class="panel">
<h3>Latest Results</h3>
<table class="history">
<thead>
<tr><th>Period</th><th>Number</th><th>Color</th><th>Size</th></tr>
</thead>
<tbody id="history">
<tr><td colspan="4">No results yet</td></tr>
</tbody>
</table>
</div>

<div class="panel">
<h3>My History</h3>
<div id="myHistory" class="muted">No activity yet</div>
</div>

</div>

<script>
let balance=1000;
let selected=null;
let seconds=30;
let period=100001;
let locked=false;

function choose(value,button){
if(locked)return;
selected=value;
document.querySelectorAll('.pick').forEach(b=>b.classList.remove('selected'));
button.classList.add('selected');
document.getElementById('message').textContent="Selected: "+value;
}

function addCoins(){
balance+=50;
updateBalance();
document.getElementById('message').textContent="50 Demo Coins added!";
}

function updateBalance(){
document.getElementById('balance').textContent=balance;
}

function play(){
if(locked)return;
if(!selected){
document.getElementById('message').textContent="Please select an option first!";
return;
}
if(balance<10){
document.getElementById('message').textContent="Not enough demo coins!";
return;
}
balance-=10;
updateBalance();

let choice=selected;
let currentPeriod=period;
let result="Pending";
locked=true;
document.getElementById('message').textContent=
"10 Demo Coins used. Result pending!";

let my=document.getElementById('myHistory');
my.innerHTML="Period "+currentPeriod+
" | Choice: "+choice+" | Result: Pending";

window.pendingChoice=choice;
window.pendingPeriod=currentPeriod;
}

function getColor(n){
if(n===0)return "Violet";
if(n===5)return "Violet";
if(n%2===0)return "Red";
return "Green";
}

function finishRound(){
let n=Math.floor(Math.random()*10);
let color=getColor(n);
let size=n>=5?"Big":"Small";

let choice=window.pendingChoice;
let won=false;

if(choice!==undefined){
if(choice===String(n)||choice===color||choice===size){
won=true;
}
}

let message="Result: "+n+" - "+color+" - "+size;

if(choice!==undefined){
if(won){
balance+=20;
message+=" | Demo win: +20 Coins";
}else{
message+=" | Demo loss: 0 Coins";
}
updateBalance();

document.getElementById('myHistory').innerHTML=
"Period "+window.pendingPeriod+
" | Your choice: "+choice+
" | Result: "+n+
" | "+(won?"Demo Win":"Demo Loss");
window.pendingChoice=undefined;
}

document.getElementById('message').textContent=message;

let row=document.createElement('tr');
row.innerHTML="<td>"+period+"</td><td>"+n+
"</td><td>"+color+"</td><td>"+size+"</td>";

let history=document.getElementById('history');
if(history.children.length===1 &&
history.children[0].textContent.includes("No results")){
history.innerHTML="";
}
history.prepend(row);

while(history.children.length>10){
history.removeChild(history.lastChild);
}

period++;
document.getElementById('period').textContent=period;
locked=false;
selected=null;
document.querySelectorAll('.pick').forEach(b=>b.classList.remove('selected'));
}

setInterval(function(){
seconds--;
if(seconds<=0){
finishRound();
seconds=30;
}
document.getElementById('timer').textContent=seconds;
},1000);
</script>

</body>
</html>
index.html# luckycolor
Lucky Color - Mobile Web Game
