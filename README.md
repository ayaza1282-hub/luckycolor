
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>Lucky Color Game</title>
<style>
*{box-sizing:border-box}
body{
 margin:0;background:#101426;color:white;
 font-family:Arial,sans-serif;padding:15px;
}
.container{max-width:480px;margin:auto}
h1{text-align:center;color:#ffd34e}
.box{
 background:#202944;padding:16px;
 border-radius:14px;margin:14px 0;
 text-align:center;
}
.wallet{font-size:25px;color:#ffd34e}
button{
 border:0;border-radius:9px;padding:13px 15px;
 margin:5px;font-size:15px;font-weight:bold;
 cursor:pointer;
}
.red{background:#f04444;color:white}
.green{background:#13b96b;color:white}
.violet{background:#963de0;color:white}
.gold{background:#ffd34e;color:#171717}
.number{background:#34415f;color:white;min-width:42px}
.selected{outline:3px solid white}
#timer{font-size:38px;color:#ffd34e;font-weight:bold}
#message{color:#ffd34e;min-height:22px}
.history{text-align:left;line-height:2}
.small{font-size:13px;color:#b9c2d8}
</style>
</head>
<body>
<div class="container">

<h1>LUCKY COLOR</h1>

<div class="box">
<h3>DEMO WALLET</h3>
<div class="wallet" id="wallet">1000 Coins</div>
<button class="gold" onclick="addCoins()">+ Add 50 Demo Coins</button>
</div>

<div class="box">
<h2>WINGO 30 SEC</h2>
<div id="timer">30</div>
<p class="small">Next result in</p>
</div>

<div class="box">
<h3>Choose Color</h3>
<button class="red choice" onclick="selectChoice('Red',this)">RED</button>
<button class="green choice" onclick="selectChoice('Green',this)">GREEN</button>
<button class="violet choice" onclick="selectChoice('Violet',this)">VIOLET</button>

<h3>Choose Number</h3>
<div id="numbers"></div>

<h3>Big / Small</h3>
<button class="gold choice" onclick="selectChoice('Big',this)">BIG</button>
<button class="gold choice" onclick="selectChoice('Small',this)">SMALL</button>

<p id="selected">Your selection: None</p>
<button class="gold" onclick="placeBet()">PLAY DEMO - 10 COINS</button>
<p id="message">Select your choice</p>
<p class="small">Demo coins only. No real money.</p>
</div>

<div class="box">
<h3>Latest Results</h3>
<div id="results">Waiting for result...</div>
</div>

<div class="box">
<h3>My History</h3>
<div id="history" class="history">No history yet</div>
</div>

</div>

<script>
let wallet=1000;
let time=30;
let choice=null;
let pending=false;
let results=[];
let history=[];

function updateWallet(){
 document.getElementById("wallet").innerText=wallet+" Coins";
}

function addCoins(){
 wallet+=50;
 updateWallet();
 document.getElementById("message").innerText="50 demo coins added!";
}

function selectChoice(value,button){
 if(pending){
  document.getElementById("message").innerText="Wait for next round";
  return;
 }
 choice=value;
 document.querySelectorAll(".choice,.number").forEach(b=>b.classList.remove("selected"));
 button.classList.add("selected");
 document.getElementById("selected").innerText="Your selection: "+value;
}

for(let i=0;i<10;i++){
 let b=document.createElement("button");
 b.className="number";
 b.innerText=i;
 b.onclick=()=>selectChoice(String(i),b);
 document.getElementById("numbers").appendChild(b);
}

function placeBet(){
 if(pending){
  document.getElementById("message").innerText="Bet already placed!";
  return;
 }
 if(!choice){
  document.getElementById("message").innerText="Please select first!";
  return;
 }
 if(wallet<10){
  document.getElementById("message").innerText="Not enough coins!";
  return;
 }
 wallet-=10;
 updateWallet();
 pending=true;
 document.getElementById("message").innerText="Your demo bet is placed!";
}

function newRound(){
 let n=Math.floor(Math.random()*10);
 let color=(n===0||n===5)?"Violet":(n%2===0?"Red":"Green");
 let size=n>=5?"Big":"Small";
 let won=pending && (
  choice===color || choice===String(n) || choice===size
 );

 if(won){
  wallet+=20;
  updateWallet();
 }

 let result={
  number:n,
  color:color,
  size:size,
  status:pending?(won?"WIN +20":"LOSS -10"):"No bet"
 };

 results.unshift(result);
 if(results.length>10)results.pop();

 history.unshift(result);
 if(history.length>20)history.pop();

 document.getElementById("results").innerHTML=
 results.map(r=>r.number+" | "+r.color+" | "+r.size).join("<br>");

 document.getElementById("history").innerHTML=
 history.map(r=>
 r.number+" | "+r.color+" | "+r.size+" | "+r.status
 ).join("<br>");

 if(pending){
  document.getElementById("message").innerText=
  won?"Congratulations! You won 20 demo coins.":"Round lost. Try again!";
 }else{
  document.getElementById("message").innerText="New result: "+n;
 }

 pending=false;
 choice=null;
 document.getElementById("selected").innerText="Your selection: None";
 document.querySelectorAll(".choice,.number").forEach(b=>b.classList.remove("selected"));
}

setInterval(()=>{
 time--;
 if(time<=0){
  newRound();
  time=30;
 }
 document.getElementById("timer").innerText=time;
},1000);

updateWallet();
</script>
</body>
</html>
