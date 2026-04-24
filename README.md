# My-game-code
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>Memory Game Levels</title>

<style>
body {
    font-family: 'Segoe UI', sans-serif;
    text-align: center;
    background: linear-gradient(135deg, #1d2671, #c33764);
    color: white;
}

.stats {
    margin: 10px;
    font-size: 18px;
}

#gameBoard {
    margin: 20px auto;
    display: grid;
    gap: 12px;
    justify-content: center;
}

.card {
    width: 80px;
    height: 80px;
    perspective: 1000px;
    cursor: pointer;
}

.inner {
    width: 100%;
    height: 100%;
    position: relative;
    transform-style: preserve-3d;
    transition: transform 0.5s;
}

.flip .inner {
    transform: rotateY(180deg);
}

.front, .back {
    position: absolute;
    width: 100%;
    height: 100%;
    border-radius: 10px;
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 24px;
    backface-visibility: hidden;
}

.front {
    background: linear-gradient(135deg, #ff9a00, #ff3c00);
}

.back {
    background: #222;
    transform: rotateY(180deg);
}

.matched .front {
    background: limegreen;
}

button {
    padding: 10px 20px;
    border: none;
    border-radius: 8px;
    background: #00c6ff;
    cursor: pointer;
}
</style>
</head>

<body>

<h1>🎮 Memory Game (Levels)</h1>

<div class="stats">
Level: <span id="level">1</span> |
Moves: <span id="moves">0</span> |
Time Left: <span id="time">30</span>s
</div>

<button onclick="startGame()">Restart</button>

<div id="gameBoard"></div>

<script>
let allSymbols = ["🍎","🍌","🍇","🍒","🍉","🍍","🥝","🍑","🥥","🍓","🍋","🍊"];

let level = 1;
let moves, matched, flipped, cards;
let timeLeft;
let timerInterval;
let gameActive = false;

function shuffle(arr) {
    return arr.sort(() => Math.random() - 0.5);
}

function getLevelConfig() {
    if (level === 1) return {pairs: 4, time: 40, cols: 4};
    if (level === 2) return {pairs: 6, time: 35, cols: 4};
    if (level === 3) return {pairs: 8, time: 30, cols: 4};
    if (level === 4) return {pairs: 10, time: 25, cols: 5};
    return {pairs: 12, time: 20, cols: 6}; // level 5
}

function startGame() {
    let config = getLevelConfig();

    let symbols = allSymbols.slice(0, config.pairs);
    cards = shuffle([...symbols, ...symbols]);

    let board = document.getElementById("gameBoard");
    board.innerHTML = "";
    board.style.gridTemplateColumns = `repeat(${config.cols}, 80px)`;

    moves = 0;
    matched = 0;
    flipped = [];
    timeLeft = config.time;
    gameActive = true;

    document.getElementById("level").textContent = level;
    document.getElementById("moves").textContent = moves;
    document.getElementById("time").textContent = timeLeft;

    clearInterval(timerInterval);
    timerInterval = setInterval(() => {
        timeLeft--;
        document.getElementById("time").textContent = timeLeft;

        if (timeLeft <= 0) {
            endGame(false);
        }
    }, 1000);

    cards.forEach(symbol => {
        let card = document.createElement("div");
        card.className = "card";
        card.dataset.symbol = symbol;

        card.innerHTML = `
            <div class="inner">
                <div class="front">${symbol}</div>
                <div class="back"></div>
            </div>
        `;

        card.onclick = () => flipCard(card);
        board.appendChild(card);
    });
}

function flipCard(card) {
    if (!gameActive || card.classList.contains("flip") || flipped.length === 2) return;

    card.classList.add("flip");
    flipped.push(card);

    if (flipped.length === 2) {
        moves++;
        document.getElementById("moves").textContent = moves;

        let a = flipped[0].dataset.symbol;
        let b = flipped[1].dataset.symbol;

        if (a === b) {
            flipped[0].classList.add("matched");
            flipped[1].classList.add("matched");

            matched += 2;
            flipped = [];

            if (matched === cards.length) {
                nextLevel();
            }

        } else {
            setTimeout(() => {
                flipped[0].classList.remove("flip");
                flipped[1].classList.remove("flip");
                flipped = [];
            }, 700);
        }
    }
}

function nextLevel() {
    clearInterval(timerInterval);
    gameActive = false;

    setTimeout(() => {
        if (level < 5) {
            level++;
            alert("🎉 Level Up! Welcome to Level " + level);
            startGame();
        } else {
            alert("🏆 You completed all levels!");
        }
    }, 300);
}

function endGame(win) {
    clearInterval(timerInterval);
    gameActive = false;

    setTimeout(() => {
        alert("⏰ Time Over! Try Again from Level 1");
        level = 1;
        startGame();
    }, 200);
}

startGame();
</script>

</body>
</html>
