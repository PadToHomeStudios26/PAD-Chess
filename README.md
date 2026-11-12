<!DOCTYPE html>
<html lang="de">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>PAD CHESS</title>
<style>
*{box-sizing:border-box;margin:0;padding:0;font-family:Arial,sans-serif}
body{background:radial-gradient(circle at top,#20213d,#080912 75%);color:white;min-height:100vh}
header{text-align:center;padding:22px;border-bottom:1px solid #682037}
header h1{font-size:clamp(32px,7vw,55px);letter-spacing:7px;color:#ff405f;text-shadow:0 0 20px #ff174c}
header p{color:#aaa;margin-top:7px;letter-spacing:4px}
main{max-width:1250px;margin:auto;padding:18px}
.screen{display:none}.screen.active{display:block}
.panel{background:#151725;border:1px solid #493044;border-radius:15px;padding:20px;margin-bottom:18px;box-shadow:0 5px 25px #0007}
h2{color:#ff647d;margin-bottom:15px}
.muted,small{color:#aeb0c5}
button{cursor:pointer;border:1px solid #ff405f;background:#a91635;color:white;padding:11px 15px;border-radius:9px;font-weight:bold;margin:3px}
button:hover{background:#e52d50}
button:disabled{opacity:.3;cursor:not-allowed}
input,select{background:#24263a;color:white;border:1px solid #555;border-radius:8px;padding:11px;width:100%}
.grid{display:grid;grid-template-columns:repeat(auto-fit,minmax(155px,1fr));gap:13px}
.card{background:#202235;border:1px solid #493044;border-radius:12px;padding:12px;text-align:center;cursor:pointer;transition:.2s}
.card:hover{transform:translateY(-4px);border-color:#ff405f}
.portrait{height:120px;border-radius:9px;display:flex;align-items:center;justify-content:center;font-size:65px;margin-bottom:10px;background:linear-gradient(145deg,#34334b,#11121c)}
.level-grid{display:grid;grid-template-columns:repeat(auto-fill,minmax(55px,1fr));gap:7px;margin-top:12px}
.level{padding:12px 4px;background:#25283c}
.level.done{background:#155b43;border-color:#36c997}
#game-layout{display:grid;grid-template-columns:minmax(0,650px) minmax(230px,1fr);gap:18px}
#board-wrap{background:#111421;border:2px solid #a52b48;border-radius:12px;padding:10px;perspective:850px}
#board{display:grid;grid-template-columns:repeat(8,1fr);width:100%;aspect-ratio:1;transition:transform .4s}
#board.perspective{transform:rotateX(28deg) scale(.94)}
.square{display:flex;align-items:center;justify-content:center;position:relative;aspect-ratio:1;font-size:clamp(25px,6vw,57px);cursor:pointer;user-select:none}
.light{background:#e2d5bd;color:#171717}.dark{background:#705346;color:white}
.square.selected{outline:4px solid #ffcf43;z-index:2}
.square.legal:after{content:"";position:absolute;width:25%;height:25%;border-radius:50%;background:#35e69a99}
.square.capture:after{content:"";position:absolute;inset:4px;border:4px solid #ff405f;border-radius:50%}
#speech{background:#292b40;border-left:4px solid #ff405f;padding:12px;border-radius:8px;min-height:60px;margin-top:12px}
#status{font-weight:bold;color:#ffd36a;margin:10px 0}
#moves{max-height:150px;overflow:auto;color:#bbb;font-family:monospace;line-height:1.7}
#editor-preview{text-align:center;font-size:65px;padding:20px;background:#25283c;border-radius:12px}
#toast{position:fixed;bottom:20px;left:50%;transform:translateX(-50%);background:#25283c;padding:12px 20px;border:1px solid #ff405f;border-radius:10px;display:none;z-index:10}
footer{text-align:center;color:#777;padding:25px}
@media(max-width:800px){#game-layout{grid-template-columns:1fr}main{padding:10px}}
</style>
</head>
<body>

<header>
<h1>PAD CHESS</h1>
<p>BY PAD TO HOME STUDIOS</p>
</header>

<main>

<section id="menu" class="screen active">
<div class="panel">
<h2>Willkommen bei PAD CHESS</h2>
<p class="muted">Dein Schach. Deine Charaktere. Deine Geschichte.</p><br>
<div class="grid">
<button onclick="showScreen('difficulty')">♟ Neues Spiel</button>
<button onclick="showScreen('story')">📖 Story-Modus</button>
<button onclick="showScreen('characters')">👥 Charaktere</button>
<button onclick="showScreen('editor')">🎨 Charakter-Editor</button>
<button onclick="showScreen('training')">🎓 Training</button>
<button onclick="showScreen('settings')">⚙ Einstellungen</button>
</div>
</div>
<div class="panel">
<h2>Dein Profil</h2>
<div id="profile-info"></div>
<p id="progress-info" class="muted"></p>
</div>
</section>

<section id="difficulty" class="screen">
<div class="panel">
<h2>Wähle deine Spielstufe</h2>
<div class="grid">
<div class="card" onclick="chooseDifficulty('Anfänger',50)"><div class="portrait">🌱</div><h3>Anfänger</h3>50 ELO</div>
<div class="card" onclick="chooseDifficulty('Kenner',200)"><div class="portrait">♟️</div><h3>Kenner</h3>200 ELO</div>
<div class="card" onclick="chooseDifficulty('Profi',500)"><div class="portrait">⚔️</div><h3>Profi</h3>500 ELO</div>
<div class="card" onclick="chooseDifficulty('Meister',700)"><div class="portrait">👑</div><h3>Meister</h3>700 ELO</div>
</div>
</div>
<button onclick="showScreen('menu')">Zurück</button>
</section>

<section id="characters" class="screen">
<div class="panel"><h2>Wähle deinen Gegner</h2><div id="character-grid" class="grid"></div></div>
<button onclick="showScreen('menu')">Zurück</button>
</section>

<section id="story" class="screen">
<div class="panel">
<h2>Story-Modus</h2>
<p class="muted">20 Kapitel · 10 Level pro Kapitel · 200 Herausforderungen</p><br>
<div id="chapter-grid" class="grid"></div>
</div>
<div class="panel"><h2 id="level-title">Kapitel auswählen</h2><div id="level-grid" class="level-grid"></div></div>
<button onclick="showScreen('menu')">Zurück</button>
</section>

<section id="editor" class="screen">
<div class="panel">
<h2>Dein Charakter</h2>
<div id="editor-preview">🧑</div><br>
<label>Name</label><input id="custom-name" maxlength="20" placeholder="Dein Name"><br><br>
<label>Hoodie-Farbe</label><input id="hoodie-color" type="color" value="#b51f43"><br><br>
<label>Haarfarbe</label><input id="hair-color" type="color" value="#222222"><br><br>
<label>Outfit</label>
<select id="outfit"><option>Hoodie</option><option>T-Shirt</option><option>Jacke</option><option>Hemd</option></select><br><br>
<button onclick="saveCharacter()">Charakter speichern</button>
<button onclick="showScreen('menu')">Zurück</button>
</div>
</section>

<section id="training" class="screen">
<div class="panel">
<h2>Schach-Tutorial</h2>
<div id="tutorial-text">Wähle eine Lektion:</div><br>
<div class="grid">
<button onclick="tutorial(0)">Bauern</button>
<button onclick="tutorial(1)">Türme</button>
<button onclick="tutorial(2)">Springer</button>
<button onclick="tutorial(3)">Läufer</button>
<button onclick="tutorial(4)">Dame</button>
<button onclick="tutorial(5)">König</button>
<button onclick="tutorial(6)">Schach & Matt</button>
<button onclick="tutorial(7)">Spezialzüge</button>
</div>
</div>
<button onclick="showScreen('menu')">Zurück</button>
</section>

<section id="settings" class="screen">
<div class="panel">
<h2>Einstellungen</h2>
<label>Kamera</label>
<select id="camera-setting" onchange="setCamera(this.value)">
<option value="top">Vogelperspektive</option>
<option value="eye">Ego-Perspektive</option>
</select><br><br>
<button onclick="resetProgress()">Spielstand zurücksetzen</button>
</div>
<button onclick="showScreen('menu')">Zurück</button>
</section>

<section id="game" class="screen">
<div id="game-layout">
<div>
<div class="panel">
<h2 id="game-title">PAD CHESS</h2>
<div id="board-wrap"><div id="board"></div></div>
<button onclick="flipBoard()">Brett drehen</button>
<button onclick="setCamera(cameraMode==='top'?'eye':'top')">Perspektive wechseln</button>
</div>
<div class="panel"><h3>Spielzüge</h3><div id="moves">Noch keine Züge.</div></div>
</div>
<aside>
<div class="panel">
<div id="opponent-emoji" style="font-size:48px">♟️</div>
<h2 id="opponent-name">Gegner</h2>
<p id="opponent-info" class="muted"></p>
<div id="speech">Bereit für eine Partie?</div>
</div>
<div class="panel">
<h3>Spielstatus</h3>
<div id="status">Weiß ist am Zug.</div>
<p id="elo-display"></p>
</div>
<div class="panel">
<button onclick="undoMove()">Zug zurück</button>
<button onclick="newGame()">Neues Spiel</button>
<button onclick="showScreen('menu')">Hauptmenü</button>
</div>
</aside>
</div>
</section>

</main>
<div id="toast"></div>
<footer>PAD CHESS · PAD TO HOME STUDIOS</footer>

<script>
// ========================================
// CHARAKTERE
// ========================================

const characters=[
["Ben","Mutig","🧑‍🦱",1],["Emma","Schnell","👩",1],
["Kai","Ruhig","🧑",1],["Zoe","Taktisch","👩‍🦰",1],
["Jonas","Neugierig","🧑‍🦱",2],["Yuki","Schnell","👩🏻",2],
["Finn","Überraschung","🧑",2],["Lara","Taktisch","👩",2],
["Max","Defensiv","🧑‍🦰",3],["Mia","Schnell","👩‍🦱",3],
["Red","Überraschung","🧑",3],["Alina","Strategisch","👩",3],
["Leon","Defensiv","🧑",4],["Nia","Aggressiv","👩‍🦰",4],
["Noah","Taktisch","🧑",4],["Luna","Strategisch","👩",4],
["Dread","Meister","🧔",5],["Lila","Meister","👩🏻",5],
["Silver","Meister","🧑‍🦳",5],["Samantha","Final Boss","👩🏻",5]
];

const quotes=["Lass uns spielen!","Interessanter Zug!","Das habe ich kommen sehen.","Du überraschst mich!","Meine Strategie funktioniert.","Das wird schwieriger als gedacht!"];

let state,history=[],selected=null;
let currentOpponent=characters[0];
let flipped=false,cameraMode="top";
let chapter=1,level=1;

let profile=JSON.parse(localStorage.getItem("padChessProfile")||"null")||{
difficulty:"Anfänger",elo:50,wins:0,completed:[],unlocked:1,
name:"Spieler",hoodie:"#b51f43",hair:"#222222",outfit:"Hoodie"
};

const $=id=>document.getElementById(id);

function saveProfile(){localStorage.setItem("padChessProfile",JSON.stringify(profile))}
function showScreen(name){
document.querySelectorAll(".screen").forEach(s=>s.classList.remove("active"));
$(name).classList.add("active");
if(name==="menu")updateProfile();
if(name==="characters")renderCharacters();
if(name==="story")renderStory();
}
function updateProfile(){
$("profile-info").textContent=profile.difficulty+" · "+profile.elo+" ELO";
$("progress-info").textContent="Siege: "+profile.wins+" · Freigeschaltete Level: "+profile.unlocked+"/200";
}
function chooseDifficulty(name,value){
profile.difficulty=name;profile.elo=value;saveProfile();showScreen("characters");
}

// ========================================
// CHARAKTERAUSWAHL
// ========================================

function renderCharacters(){
$("character-grid").innerHTML="";
characters.forEach(c=>{
let card=document.createElement("div");
card.className="card";
card.innerHTML=`<div class="portrait">${c[2]}</div><h3>${c[0]}</h3><small>${c[1]} · ${c[3]} Sterne</small><br><br><button>Gegner wählen</button>`;
card.onclick=()=>{currentOpponent=c;startGame(false)};
$("character-grid").appendChild(card);
});
}

// ========================================
// STORY
// ========================================

function renderStory(){
let box=$("chapter-grid");box.innerHTML="";
for(let i=1;i<=20;i++){
let c=document.createElement("div");c.className="card";
c.innerHTML=`<div class="portrait">♟️</div><h3>Kapitel ${i}</h3><small>${i<=profile.unlocked?"Freigeschaltet":"Gesperrt"}</small>`;
c.onclick=()=>selectChapter(i);box.appendChild(c);
}
selectChapter(Math.min(chapter,Math.ceil(profile.unlocked/10)));
}
function selectChapter(n){
chapter=n;$("level-title").textContent="Kapitel "+n+" · Level auswählen";
let box=$("level-grid");box.innerHTML="";
for(let i=1;i<=10;i++){
let number=(n-1)*10+i;
let unlocked=number<=profile.unlocked;
let done=profile.completed.includes(number);
let b=document.createElement("button");
b.className="level "+(done?"done":"");
b.textContent=i;b.disabled=!unlocked;
b.onclick=()=>{level=i;currentOpponent=characters[Math.min(19,Math.floor((number-1)/10))];startGame(true)};
box.appendChild(b);
}
}

// ========================================
// TUTORIAL
// ========================================

const lessons=[
"Bauern ziehen ein Feld nach vorne. Vom Startfeld dürfen sie zwei Felder ziehen. Sie schlagen diagonal.",
"Türme ziehen beliebig weit horizontal oder vertikal.",
"Springer ziehen in L-Form: zwei Felder in eine Richtung und eines zur Seite.",
"Läufer ziehen beliebig weit diagonal.",
"Die Dame kombiniert die Bewegungen von Turm und Läufer.",
"Der König zieht ein Feld in jede Richtung. Er darf niemals im Schach stehen bleiben.",
"Schach bedeutet, dass der König angegriffen wird. Schachmatt bedeutet, dass kein legaler Ausweg mehr existiert.",
"Spezialzüge sind Rochade, En passant und Bauernumwandlung."
];
function tutorial(i){$("tutorial-text").textContent=lessons[i]}

// ========================================
// SCHACHLOGIK
// ========================================

const initialBoard=[
"rnbqkbnr","pppppppp","........","........",
"........","........","PPPPPPPP","RNBQKBNR"
];
const values={p:100,n:320,b:330,r:500,q:900,k:20000};

function newPosition(){
return {board:initialBoard.map(x=>x.split("")),turn:"w",
castle:{K:true,Q:true,k:true,q:true},ep:null};
}
function color(x){
if(!x||x===".")return null;
return x===x.toUpperCase()?"w":"b";
}
function inside(r,c){return r>=0&&r<8&&c>=0&&c<8}
function clonePosition(p){
return {board:p.board.map(r=>r.slice()),turn:p.turn,castle:{...p.castle},ep:p.ep?{...p.ep}:null};
}
function findKing(p,side){
for(let r=0;r<8;r++)for(let c=0;c<8;c++)
if(p.board[r][c]===(side==="w"?"K":"k"))return [r,c];
return null;
}
function attacked(p,r,c,by){
let b=p.board;
let dir=by==="w"?1:-1;
for(let dc of [-1,1]){
let rr=r+dir,cc=c+dc;
if(inside(rr,cc)&&b[rr][cc]===(by==="w"?"P":"p"))return true;
}
for(let [dr,dc] of [[-2,-1],[-2,1],[-1,-2],[-1,2],[1,-2],[1,2],[2,-1],[2,1]]){
let rr=r+dr,cc=c+dc;
if(inside(rr,cc)&&b[rr][cc]===(by==="w"?"N":"n"))return true;
}
for(let [dr,dc] of [[1,0],[-1,0],[0,1],[0,-1],[1,1],[1,-1],[-1,1],[-1,-1]]){
let rr=r+dr,cc=c+dc,d=1;
while(inside(rr,cc)){
let x=b[rr][cc];
if(x!=="."){
if(color(x)===by){
let t=x.toLowerCase();
if(t==="q"||(d===1&&t==="k")||
(d===1&&((dr===0||dc===0)?t==="r":t==="b")))return true;
}
break;
}
rr+=dr;cc+=dc;d++;
}
}
return false;
}
function inCheck(p,side){
let k=findKing(p,side);
return k?attacked(p,k[0],k[1],side==="w"?"b":"w"):true;
}

// Alle möglichen Züge generieren
function pseudoMoves(p){
let moves=[],b=p.board,side=p.turn;
function add(r,c,rr,cc,extra={}){
if(!inside(rr,cc)||color(b[rr][cc])===side||b[rr][cc].toLowerCase()==="k")return;
moves.push({r,c,rr,cc,...extra});
}
for(let r=0;r<8;r++)for(let c=0;c<8;c++){
let piece=b[r][c];if(color(piece)!==side)continue;
let t=piece.toLowerCase();

if(t==="p"){
let d=side==="w"?-1:1,start=side==="w"?6:1,last=side==="w"?0:7;
if(inside(r+d,c)&&b[r+d][c]==="."){
add(r,c,r+d,c,{promotion:r+d===last});
if(r===start&&b[r+2*d][c]===".")add(r,c,r+2*d,c,{double:true});
}
for(let dc of [-1,1]){
let rr=r+d,cc=c+dc;
if(inside(rr,cc)&&color(b[rr][cc])===(side==="w"?"b":"w"))add(r,c,rr,cc,{promotion:rr===last});
if(p.ep&&p.ep.r===rr&&p.ep.c===cc)add(r,c,rr,cc,{ep:true});
}
}
if(t==="n"){
for(let [dr,dc] of [[-2,-1],[-2,1],[-1,-2],[-1,2],[1,-2],[1,2],[2,-1],[2,1]])add(r,c,r+dr,c+dc);
}
if(t==="k"){
for(let dr=-1;dr<=1;dr++)for(let dc=-1;dc<=1;dc++)if(dr||dc)add(r,c,r+dr,c+dc);
if(side==="w"&&r===7&&c===4&&!inCheck(p,"w")){
if(p.castle.K&&b[7][5]==="."&&b[7][6]==="."&&b[7][7]==="R"&&!attacked(p,7,5,"b")&&!attacked(p,7,6,"b"))add(r,c,7,6,{castle:"K"});
if(p.castle.Q&&b[7][1]==="."&&b[7][2]==="."&&b[7][3]==="."&&b[7][0]==="R"&&!attacked(p,7,3,"b")&&!attacked(p,7,2,"b"))add(r,c,7,2,{castle:"Q"});
}
if(side==="b"&&r===0&&c===4&&!inCheck(p,"b")){
if(p.castle.k&&b[0][5]==="."&&b[0][6]==="."&&b[0][7]==="r"&&!attacked(p,0,5,"w")&&!attacked(p,0,6,"w"))add(r,c,0,6,{castle:"k"});
if(p.castle.q&&b[0][1]==="."&&b[0][2]==="."&&b[0][3]==="."&&b[0][0]==="r"&&!attacked(p,0,3,"w")&&!attacked(p,0,2,"w"))add(r,c,0,2,{castle:"q"});
}
}
if(["r","b","q"].includes(t)){
let dirs=[];
if(t!=="b")dirs.push([1,0],[-1,0],[0,1],[0,-1]);
if(t!=="r")dirs.push([1,1],[1,-1],[-1,1],[-1,-1]);
for(let [dr,dc] of dirs){
let rr=r+dr,cc=c+dc;
while(inside(rr,cc)){
if(color(b[rr][cc])===side)break;
add(r,c,rr,cc);
if(b[rr][cc]!==".")break;
rr+=dr;cc+=dc;
}
}
}
}
return moves;
}

function applyMove(p,m){
let q=clonePosition(p),piece=q.board[m.r][m.c];
q.board[m.r][m.c]=".";
if(m.ep)q.board[m.r][m.cc]= ".";
if(m.promotion)piece=piece==="P"?"Q":"q";
q.board[m.rr][m.cc]=piece;

if(m.castle==="K"){q.board[7][7]=".";q.board[7][5]="R"}
if(m.castle==="Q"){q.board[7][0]=".";q.board[7][3]="R"}
if(m.castle==="k"){q.board[0][7]=".";q.board[0][5]="r"}
if(m.castle==="q"){q.board[0][0]=".";q.board[0][3]="r"}

if(piece==="K"){q.castle.K=false;q.castle.Q=false}
if(piece==="k"){q.castle.k=false;q.castle.q=false}
if(m.r===7&&m.c===0)q.castle.Q=false;
if(m.r===7&&m.c===7)q.castle.K=false;
if(m.r===0&&m.c===0)q.castle.q=false;
if(m.r===0&&m.c===7)q.castle.k=false;

q.ep=m.double?{r:(m.r+m.rr)/2,c:m.c}:null;
q.turn=p.turn==="w"?"b":"w";
return q;
}
function legalMovesFor(p){
return pseudoMoves(p).filter(m=>!inCheck(applyMove(p,m),p.turn));
}

// ========================================
// KI
// ========================================

function evaluate(p){
let score=0;
for(let r=0;r<8;r++)for(let c=0;c<8;c++){
let x=p.board[r][c];if(x===".")continue;
let v=values[x.toLowerCase()]||0;
v+=(3.5-(Math.abs(3.5-r)+Math.abs(3.5-c))/2)*3;
score+=color(x)==="w"?v:-v;
}
return score;
}
function minimax(p,depth,alpha,beta){
let moves=legalMovesFor(p);
if(depth===0)return evaluate(p)*(p.turn==="w"?1:-1);
if(!moves.length)return inCheck(p,p.turn)?-100000:0;
let best=-Infinity;
for(let m of moves){
let value=-minimax(applyMove(p,m),depth-1,-beta,-alpha);
best=Math.max(best,value);
alpha=Math.max(alpha,value);
if(alpha>=beta)break;
}
return best;
}
function chooseAI(p){
let moves=legalMovesFor(p);
if(!moves.length)return null;
let depth=profile.difficulty==="Anfänger"?1:
profile.difficulty==="Kenner"?2:
profile.difficulty==="Profi"?2:3;
let best=-Infinity,bestMove=moves[0];
for(let m of moves){
let score=-minimax(applyMove(p,m),depth-1,-Infinity,Infinity);
score+=Math.random()*2;
if(score>best){best=score;bestMove=m}
}
return bestMove;
}

// ========================================
// SPIELSTART
// ========================================

function startGame(isStory=false){
state=newPosition();history=[];selected=null;
$("game-title").textContent=isStory?"Kapitel "+chapter+" · Level "+level:"Freies Spiel";
$("opponent-name").textContent=currentOpponent[0];
$("opponent-emoji").textContent=currentOpponent[2];
$("opponent-info").textContent=currentOpponent[1]+" · "+currentOpponent[3]+" Sterne";
$("speech").textContent=currentOpponent[0]+": "+quotes[Math.floor(Math.random()*quotes.length)];
$("elo-display").textContent=profile.elo+" ELO · "+profile.difficulty;
$("moves").textContent="Noch keine Züge.";
showScreen("game");renderBoard();updateStatus();
}

// ========================================
// BRETTDARSTELLUNG
// ========================================

const symbols={K:"♔",Q:"♕",R:"♖",B:"♗",N:"♘",P:"♙",k:"♚",q:"♛",r:"♜",b:"♝",n:"♞",p:"♟"};

function renderBoard(){
let board=$("board");board.innerHTML="";
let moves=selected?legalMovesFor(state).filter(m=>m.r===selected[0]&&m.c===selected[1]):[];
for(let i=0;i<64;i++){
let r=Math.floor(i/8),c=i%8;
let rr=flipped?7-r:r,cc=flipped?7-c:c;
let piece=state.board[rr][cc];
let sq=document.createElement("div");
sq.className="square "+((rr+cc)%2===0?"light":"dark");
if(selected&&selected[0]===rr&&selected[1]===cc)sq.classList.add("selected");
if(moves.some(m=>m.rr===rr&&m.cc===cc))sq.classList.add(piece==="."?"legal":"capture");
if(piece!=="."){let span=document.createElement("span");span.textContent=symbols[piece];sq.appendChild(span)}
sq.onclick=()=>clickSquare(rr,cc);
board.appendChild(sq);
}
}

function clickSquare(r,c){
if(state.turn!=="w")return;
let piece=state.board[r][c];
if(selected){
let move=legalMovesFor(state).find(m=>m.r===selected[0]&&m.c===selected[1]&&m.rr===r&&m.cc===c);
if(move){
history.push(clonePosition(state));
state=applyMove(state,move);
selected=null;renderBoard();updateStatus();
if(finishIfNeeded())return;
$("speech").textContent=currentOpponent[0]+": "+quotes[Math.floor(Math.random()*quotes.length)];
setTimeout(aiTurn,300);
return;
}
}
selected=color(piece)==="w"?[r,c]:null;
renderBoard();
}

function aiTurn(){
if(state.turn!=="b"||finishIfNeeded())return;
let move=chooseAI(state);
if(!move)return;
history.push(clonePosition(state));
state=applyMove(state,move);
renderBoard();updateStatus();
if(finishIfNeeded())return;
$("speech").textContent=currentOpponent[0]+": "+quotes[Math.floor(Math.random()*quotes.length)];
}

function finishIfNeeded(){
let moves=legalMovesFor(state);
if(moves.length)return false;
if(inCheck(state,state.turn)){
if(state.turn==="b"){
$("status").textContent="Schachmatt! Du hast gewonnen!";
profile.wins++;
profile.elo+=12;
let number=(chapter-1)*10+level;
if(!profile.completed.includes(number))profile.completed.push(number);
profile.unlocked=Math.max(profile.unlocked,Math.min(200,number+1));
saveProfile();
}else $("status").textContent="Schachmatt! Du hast verloren.";
}else $("status").textContent="Patt! Unentschieden.";
$("speech").textContent="Die Partie ist beendet.";
return true;
}
function updateStatus(){
$("status").textContent=inCheck(state,state.turn)?
(state.turn==="w"?"Weiß steht im Schach!":"Schwarz steht im Schach!"):
(state.turn==="w"?"Du bist am Zug.":"Gegner denkt nach...");
}
function undoMove(){
if(!history.length)return;
state=history.pop();
if(history.length&&state.turn==="b")state=history.pop();
selected=null;renderBoard();updateStatus();
}
function newGame(){startGame(false)}
function flipBoard(){flipped=!flipped;renderBoard()}

// ========================================
// KAMERA
// ========================================

function setCamera(mode){
cameraMode=mode;
$("camera-setting").value=mode;
$("board").classList.toggle("perspective",mode==="eye");
}

// ========================================
// CHARAKTEREDITOR
// ========================================

function saveCharacter(){
profile.name=$("custom-name").value||"Spieler";
profile.hoodie=$("hoodie-color").value;
profile.hair=$("hair-color").value;
profile.outfit=$("outfit").value;
saveProfile();
$("editor-preview").style.background="linear-gradient(145deg,"+profile.hoodie+",#171725)";
toast("Charakter gespeichert!");
}
function resetProgress(){
if(!confirm("Wirklich alle Fortschritte zurücksetzen?"))return;
localStorage.removeItem("padChessProfile");location.reload();
}

// ========================================
// INITIALISIERUNG
// ========================================

function toast(text){
$("toast").textContent=text;
$("toast").style.display="block";
setTimeout(()=>$("toast").style.display="none",2200);
}

$("custom-name").value=profile.name;
$("hoodie-color").value=profile.hoodie;
$("hair-color").value=profile.hair;
$("outfit").value=profile.outfit;
$("editor-preview").style.background="linear-gradient(145deg,"+profile.hoodie+",#171725)";
updateProfile();
renderCharacters();

</script>
</body>
</html>