<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Memory Match Challenge</title>

<style>
*{
    margin:0;
    padding:0;
    box-sizing:border-box;
    font-family:'Segoe UI',sans-serif;
}

body{
    min-height:100vh;
    background:linear-gradient(135deg,#667eea,#764ba2);
    display:flex;
    flex-direction:column;
    align-items:center;
    color:white;
    padding:20px;
}

h1{
    margin-bottom:10px;
    text-align:center;
}

.info{
    display:flex;
    gap:30px;
    margin-bottom:20px;
    font-size:22px;
    font-weight:bold;
}

.game{
    display:grid;
    grid-template-columns:repeat(5,90px);
    gap:12px;
}

.card{
    width:90px;
    height:90px;
    position:relative;
    transform-style:preserve-3d;
    transition:transform 0.5s;
    cursor:pointer;
}

.card.flip{
    transform:rotateY(180deg);
}

.front,
.back{
    position:absolute;
    width:100%;
    height:100%;
    border-radius:15px;
    display:flex;
    justify-content:center;
    align-items:center;
    font-size:40px;
    backface-visibility:hidden;
}

.front{
    background:white;
    color:#333;
    transform:rotateY(180deg);
}

.back{
    background:#ff4d6d;
    color:white;
}

#message{
    margin-top:20px;
    font-size:30px;
    font-weight:bold;
    text-align:center;
}

@media(max-width:600px){

.game{
    grid-template-columns:repeat(4,70px);
}

.card{
    width:70px;
    height:70px;
}

.front,.back{
    font-size:30px;
}
}
</style>
</head>
<body>

<h1>🧠 Memory Match Game</h1>

<div class="info">
    <div>⏳ <span id="timer">50</span>s</div>
    <div>✅ <span id="pairs">0</span>/10</div>
</div>

<div class="game" id="game"></div>

<div id="message"></div>

<script>

const emojis = [
"🍎","🍎",
"🍕","🍕",
"🚗","🚗",
"⚽","⚽",
"🎮","🎮",
"🐱","🐱",
"🌟","🌟",
"🎵","🎵",
"🍩","🍩",
"❤️","❤️"
];

let firstCard=null;
let secondCard=null;
let lockBoard=false;
let matchedPairs=0;
let timeLeft=50;
let timer;

const game=document.getElementById("game");

function shuffle(array){
    for(let i=array.length-1;i>0;i--){
        let j=Math.floor(Math.random()*(i+1));
        [array[i],array[j]]=[array[j],array[i]];
    }
}

function startGame(){

    game.innerHTML="";
    matchedPairs=0;
    document.getElementById("pairs").textContent=0;
    document.getElementById("message").textContent="";

    let cards=[...emojis];
    shuffle(cards);

    cards.forEach(emoji=>{

        const card=document.createElement("div");
        card.classList.add("card");
        card.dataset.emoji=emoji;

        card.innerHTML=`
        <div class="front">${emoji}</div>
        <div class="back">?</div>
        `;

        card.addEventListener("click",flipCard);

        game.appendChild(card);
    });

    startTimer();
}

function flipCard(){

    if(lockBoard) return;
    if(this===firstCard) return;
    if(this.classList.contains("matched")) return;

    this.classList.add("flip");

    if(!firstCard){
        firstCard=this;
        return;
    }

    secondCard=this;
    lockBoard=true;

    checkMatch();
}

function checkMatch(){

    const match=
    firstCard.dataset.emoji===
    secondCard.dataset.emoji;

    if(match){

        firstCard.classList.add("matched");
        secondCard.classList.add("matched");

        matchedPairs++;

        document.getElementById("pairs")
        .textContent=matchedPairs;

        resetBoard();

        if(matchedPairs===10){

            clearInterval(timer);

            document.getElementById("message")
            .innerHTML="🎉 YOU WIN! 🎉";
        }

    }else{

        setTimeout(()=>{

            firstCard.classList.remove("flip");
            secondCard.classList.remove("flip");

            resetBoard();

        },1000);
    }
}

function resetBoard(){
    [firstCard,secondCard,lockBoard]=
    [null,null,false];
}

function startTimer(){

    clearInterval(timer);

    timeLeft=50;

    document.getElementById("timer")
    .textContent=timeLeft;

    timer=setInterval(()=>{

        timeLeft--;

        document.getElementById("timer")
        .textContent=timeLeft;

        if(timeLeft<=0){

            clearInterval(timer);

            document.getElementById("message")
            .innerHTML="⏰ Time Up! Restarting...";

            setTimeout(()=>{
                startGame();
            },2000);
        }

    },1000);
}

startGame();

</script>

</body>
</html>
