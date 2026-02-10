
<html lang="id">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0"/>
<title> Just For Example hehe </title>
<style>
body{
    margin:0;
    background:#0f0f0f;
    color:white;
    font-family:Arial, Helvetica, sans-serif;
    text-align:center;
}
h2{margin:10px 0}

#top{
    font-size:14px;
    margin-bottom:6px;
}

#bar{
    width:92%;
    height:10px;
    background:#222;
    margin:8px auto;
    border-radius:10px;
    overflow:hidden;
}
#barFill{
    height:100%;
    width:100%;
    background:#00ff88;
    transition:width 1s linear;
}

.grid{
    display:grid;
    gap:8px;
    padding:10px;
    max-width:560px;
    margin:auto;
}

.card{
    perspective:800px;
    aspect-ratio:1/1;
}

.inner{
    position:relative;
    width:100%;
    height:100%;
    transform-style:preserve-3d;
    transition:transform .5s;
    border-radius:12px;
}

.card.open .inner{
    transform:rotateY(180deg);
}

.front,.back{
    position:absolute;
    width:100%;
    height:100%;
    border-radius:12px;
    display:flex;
    align-items:center;
    justify-content:center;
    font-size:28px;
    backface-visibility:hidden;
}

.front{
    background:#444;
}
.back{
    background:white;
    color:black;
    transform:rotateY(180deg);
}

button{
    margin:6px;
    padding:6px 14px;
    border:none;
    border-radius:6px;
    cursor:pointer;
}
</style>
</head>
<body>

<h2>just For Example Hehew</h2>

<div id="top">
Level: <span id="level">1</span> |
Salah: <span id="mistake">0</span> |
Combo: <span id="combo">0</span> |
Skor: <span id="score">0</span> |
High Score: <span id="high">0</span>
</div>

<div id="bar"><div id="barFill"></div></div>
<div id="grid" class="grid"></div>

<button id="restart">Restart</button>

<script>
const emojis=["🍎","🍌","🍓","🍇","🍒","🥝","🍍","🍉","🥥","🍑","🥕","🌽","🍆","🥔","🥦","🌶️","🍄","🥜","🍋","🥭","🥑"];

const audioCtx = new (window.AudioContext||window.webkitAudioContext)();
function beep(freq){
    const o=audioCtx.createOscillator();
    o.type="sine";
    o.frequency.value=freq;
    o.connect(audioCtx.destination);
    o.start();
    setTimeout(()=>o.stop(),80);
}

function shuffle(a){
    for(let i=a.length-1;i>0;i--){
        const j=Math.floor(Math.random()*(i+1));
        [a[i],a[j]]=[a[j],a[i]];
    }
    return a;
}

let level=1;
let first=null;
let lock=true;
let matches=0;
let mistake=0;
let combo=0;
let score=0;
let timer, remaining, timeLimit;

const grid=document.getElementById("grid");
const levelEl=document.getElementById("level");
const mistakeEl=document.getElementById("mistake");
const comboEl=document.getElementById("combo");
const scoreEl=document.getElementById("score");
const highEl=document.getElementById("high");
const barFill=document.getElementById("barFill");

let highScore=localStorage.getItem("pairHigh")||0;
highEl.textContent=highScore;

function startLevel(){
    clearInterval(timer);
    grid.innerHTML="";
    first=null;
    lock=true;
    matches=0;
    mistake=0;
    combo=0;

    levelEl.textContent=level;
    mistakeEl.textContent=0;
    comboEl.textContent=0;

    const pairs = Math.min(3+level, 12);
    const cols = Math.ceil(Math.sqrt(pairs*2));
    timeLimit = 15 + level*6;

    grid.style.gridTemplateColumns=`repeat(${cols},1fr)`;

    const chosen=shuffle([...emojis]).slice(0,pairs);
    const cards=shuffle([...chosen,...chosen]);

    cards.forEach(e=>{
        const c=document.createElement("div");
        c.className="card";

        const inner=document.createElement("div");
        inner.className="inner";

        const front=document.createElement("div");
        front.className="front";
        front.textContent="";

        const back=document.createElement("div");
        back.className="back";
        back.textContent=e;

        inner.append(front,back);
        c.appendChild(inner);

        c.onclick=()=>tapCard(c,e);
        grid.appendChild(c);
    });

    // preview
    document.querySelectorAll(".card").forEach(c=>c.classList.add("open"));

    setTimeout(()=>{
        document.querySelectorAll(".card").forEach(c=>c.classList.remove("open"));
        lock=false;
        startTimer();
    },2000);
}

function startTimer(){
    remaining=timeLimit;
    barFill.style.width="100%";
    timer=setInterval(()=>{
        remaining--;
        barFill.style.width=(remaining/timeLimit*100)+"%";
        if(remaining<=0){
            clearInterval(timer);
            alert("Lambatnya Pang");
            startLevel();
        }
    },1000);
}

function tapCard(card,emoji){
    if(lock || card.classList.contains("open")) return;

    card.classList.add("open");

    if(!first){
        first={card,emoji};
    }else{
        if(first.emoji===emoji){
            beep(800);
            combo++;
            matches++;
            score += 50 + combo*10 + remaining;
            scoreEl.textContent=score;
            comboEl.textContent=combo;

            first.card.onclick=null;
            card.onclick=null;
            first=null;

            if(matches===Math.min(3+level,12)){
                clearInterval(timer);
                level++;
                updateHigh();
                setTimeout(startLevel,1200);
            }
        }else{
            beep(200);
            combo=0;
            comboEl.textContent=0;
            mistake++;
            mistakeEl.textContent=mistake;
            lock=true;

            setTimeout(()=>{
                card.classList.remove("open");
                first.card.classList.remove("open");
                first=null;
                lock=false;
            },600);
        }
    }
}

function updateHigh(){
    if(score>highScore){
        highScore=score;
        localStorage.setItem("pairHigh",highScore);
        highEl.textContent=highScore;
    }
}

document.getElementById("restart").onclick=()=>{
    level=1;
    score=0;
    scoreEl.textContent=0;
    startLevel();
};
startLevel();
</script>
</body>
</html>
.grid{
    display:grid;
    gap:8px;
    padding:10px;
    max-width:560px;
    margin:auto;
}

.card{
    perspective:800px;
    aspect-ratio:1/1;
}

.inner{
    position:relative;
    width:100%;
    height:100%;
    transform-style:preserve-3d;
    transition:transform .5s;
    border-radius:12px;
}

.card.open .inner{
    transform:rotateY(180deg);
}

.front,.back{
    position:absolute;
    width:100%;
    height:100%;
    border-radius:12px;
    display:flex;
    align-items:center;
    justify-content:center;
    font-size:28px;
    backface-visibility:hidden;
}

.front{
    background:#444;
}
.back{
    background:white;
    color:black;
    transform:rotateY(180deg);
}

button{
    margin:6px;
    padding:6px 14px;
    border:none;
    border-radius:6px;
    cursor:pointer;
}
</style>
</head>
<body>

<h2>Tap The Pair – PRO DEV</h2>

<div id="top">
Level: <span id="level">1</span> |
Salah: <span id="mistake">0</span> |
Combo: <span id="combo">0</span> |
Skor: <span id="score">0</span> |
High Score: <span id="high">0</span>
</div>

<div id="bar"><div id="barFill"></div></div>
<div id="grid" class="grid"></div>

<button id="restart">Restart</button>

<script>
const emojis=["🍎","🍌","🍓","🍇","🍒","🥝","🍍","🍉","🥥","🍑","🥕","🌽","🍆","🥔","🥦","🌶️","🍄","🥜","🍋","🥭","🥑"];

const audioCtx = new (window.AudioContext||window.webkitAudioContext)();
function beep(freq){
    const o=audioCtx.createOscillator();
    o.type="sine";
    o.frequency.value=freq;
    o.connect(audioCtx.destination);
    o.start();
    setTimeout(()=>o.stop(),80);
}

function shuffle(a){
    for(let i=a.length-1;i>0;i--){
        const j=Math.floor(Math.random()*(i+1));
        [a[i],a[j]]=[a[j],a[i]];
    }
    return a;
}

let level=1;
let first=null;
let lock=true;
let matches=0;
let mistake=0;
let combo=0;
let score=0;
let timer, remaining, timeLimit;

const grid=document.getElementById("grid");
const levelEl=document.getElementById("level");
const mistakeEl=document.getElementById("mistake");
const comboEl=document.getElementById("combo");
const scoreEl=document.getElementById("score");
const highEl=document.getElementById("high");
const barFill=document.getElementById("barFill");

let highScore=localStorage.getItem("pairHigh")||0;
highEl.textContent=highScore;

function startLevel(){
    clearInterval(timer);
    grid.innerHTML="";
    first=null;
    lock=true;
    matches=0;
    mistake=0;
    combo=0;

    levelEl.textContent=level;
    mistakeEl.textContent=0;
    comboEl.textContent=0;

    const pairs = Math.min(3+level, 12);
    const cols = Math.ceil(Math.sqrt(pairs*2));
    timeLimit = 15 + level*6;

    grid.style.gridTemplateColumns=`repeat(${cols},1fr)`;

    const chosen=shuffle([...emojis]).slice(0,pairs);
    const cards=shuffle([...chosen,...chosen]);

    cards.forEach(e=>{
        const c=document.createElement("div");
        c.className="card";

        const inner=document.createElement("div");
        inner.className="inner";

        const front=document.createElement("div");
        front.className="front";
        front.textContent="❓";

        const back=document.createElement("div");
        back.className="back";
        back.textContent=e;

        inner.append(front,back);
        c.appendChild(inner);

        c.onclick=()=>tapCard(c,e);
        grid.appendChild(c);
    });

    // preview
    document.querySelectorAll(".card").forEach(c=>c.classList.add("open"));

    setTimeout(()=>{
        document.querySelectorAll(".card").forEach(c=>c.classList.remove("open"));
        lock=false;
        startTimer();
    },2000);
}

function startTimer(){
    remaining=timeLimit;
    barFill.style.width="100%";
    timer=setInterval(()=>{
        remaining--;
        barFill.style.width=(remaining/timeLimit*100)+"%";
        if(remaining<=0){
            clearInterval(timer);
            alert("Waktu habis. Level diulang.");
            startLevel();
        }
    },1000);
}

function tapCard(card,emoji){
    if(lock || card.classList.contains("open")) return;

    card.classList.add("open");

    if(!first){
        first={card,emoji};
    }else{
        if(first.emoji===emoji){
            beep(800);
            combo++;
            matches++;
            score += 50 + combo*10 + remaining;
            scoreEl.textContent=score;
            comboEl.textContent=combo;

            first.card.onclick=null;
            card.onclick=null;
            first=null;

            if(matches===Math.min(3+level,12)){
                clearInterval(timer);
                level++;
                updateHigh();
                setTimeout(startLevel,1200);
            }
        }else{
            beep(200);
            combo=0;
            comboEl.textContent=0;
            mistake++;
            mistakeEl.textContent=mistake;
            lock=true;

            setTimeout(()=>{
                card.classList.remove("open");
                first.card.classList.remove("open");
                first=null;
                lock=false;
            },600);
        }
    }
}

function updateHigh(){
    if(score>highScore){
        highScore=score;
        localStorage.setItem("pairHigh",highScore);
        highEl.textContent=highScore;
    }
}

document.getElementById("restart").onclick=()=>{
    level=1;
    score=0;
    scoreEl.textContent=0;
    startLevel();
};

startLevel();
</script>
</body>
</html>
