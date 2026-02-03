<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>Neon Cyber Tetris</title>
    <style>
        body {
            margin: 0;
            background: #0a0a0f; /* 深い紺色 */
            color: #fff;
            font-family: 'Segoe UI', sans-serif;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            overflow: hidden;
        }

        #app {
            display: flex;
            gap: 30px;
            padding: 30px;
            background: rgba(20, 20, 35, 0.8);
            border: 2px solid #333;
            border-radius: 10px;
            box-shadow: 0 0 20px rgba(0, 0, 0, 0.5);
        }

        /* ゲーム画面 */
        canvas {
            background: #000;
            border: 2px solid #444;
            box-shadow: 0 0 15px rgba(255, 255, 255, 0.05);
        }

        /* サイドパネル */
        .side-panel {
            width: 200px;
            display: flex;
            flex-direction: column;
            gap: 25px;
        }

        /* ネオンUIパネル */
        .neon-box {
            border: 2px solid #555;
            padding: 20px;
            background: rgba(0, 0, 0, 0.3);
            border-radius: 5px;
            position: relative;
            box-shadow: inset 0 0 10px rgba(255, 255, 255, 0.05);
        }

        .score-box { border-color: #0ff; box-shadow: 0 0 10px rgba(0, 255, 255, 0.2); }
        .control-box { border-color: #f0f; box-shadow: 0 0 10px rgba(255, 0, 255, 0.2); font-size: 13px; line-height: 1.6; }

        .label {
            position: absolute;
            top: -10px;
            left: 15px;
            background: #0a0a0f;
            padding: 0 8px;
            font-size: 12px;
            font-weight: bold;
            letter-spacing: 2px;
        }

        .score-label { color: #0ff; text-shadow: 0 0 5px #0ff; }
        .ctrl-label { color: #f0f; text-shadow: 0 0 5px #f0f; }

        .value {
            font-size: 36px;
            text-align: center;
            display: block;
            color: #fff;
            text-shadow: 0 0 10px rgba(255, 255, 255, 0.5);
        }

        /* ゲームオーバー */
        #overlay {
            position: absolute; inset: 0; 
            background: rgba(0, 0, 0, 0.9);
            display: none; justify-content: center; align-items: center;
            flex-direction: column; z-index: 100;
        }

        h1 { font-size: 48px; margin: 0; color: #fff; text-shadow: 0 0 20px #f00; }

        button {
            margin-top: 30px; padding: 12px 40px; 
            background: transparent; color: #fff;
            border: 2px solid #fff; border-radius: 5px;
            font-size: 18px; cursor: pointer;
            transition: all 0.3s;
        }
        button:hover { background: #fff; color: #000; box-shadow: 0 0 20px #fff; }
    </style>
</head>
<body>

<div id="app">
    <div id="overlay">
        <h1>SYSTEM FAILURE</h1>
        <div id="finalScore" style="font-size: 24px; margin-top: 10px;"></div>
        <button onclick="location.reload()">REBOOT</button>
    </div>

    <canvas id="game" width="300" height="600"></canvas>

    <div class="side-panel">
        <div class="neon-box score-box">
            <span class="label score-label">DATA_SCORE</span>
            <span id="score" class="value">0</span>
        </div>
        
        <div class="neon-box control-box">
            <span class="label ctrl-label">COMMANDS</span>
            ← → : SHIFT<br>
            ↑ : ROTATE<br>
            ↓ : ACCELERATE<br>
            SPACE: INSTANT_DROP
        </div>
    </div>
</div>

<script>
const COLS = 10, ROWS = 20, SIZE = 30;
const SHAPES = [
    [[1, 1, 1, 1]], [[1, 1], [1, 1]], [[0, 1, 0], [1, 1, 1]],
    [[1, 1, 0], [0, 1, 1]], [[0, 1, 1], [1, 1, 0]],
    [[1, 0, 0], [1, 1, 1]], [[0, 0, 1], [1, 1, 1]]
];

// ネオンカラーの定義
const COLORS = [
    "#00ffff", // Cyan
    "#ffff00", // Yellow
    "#ff00ff", // Magenta
    "#00ff00", // Lime
    "#ff0000", // Red
    "#4444ff", // Blue
    "#ff8800"  // Orange
];

const Game = {
    ctx: document.getElementById("game").getContext("2d"),
    score: 0,
    over: false,
    grid: [],
    piece: null,
    dropCounter: 0,
    dropInterval: 800,
    lastTime: 0,

    init() {
        this.ctx.scale(SIZE, SIZE);
        this.grid = Array.from({length: ROWS}, () => Array(COLS).fill(0));
        this.spawn();
        requestAnimationFrame(this.update.bind(this));
    },

    spawn() {
        const id = Math.random() * SHAPES.length | 0;
        this.piece = {
            matrix: JSON.parse(JSON.stringify(SHAPES[id])),
            color: COLORS[id],
            pos: {x: (COLS / 2 | 0) - (SHAPES[id][0].length / 2 | 0), y: 0}
        };
        if (this.collide()) {
            this.over = true;
            document.getElementById("finalScore").innerText = "SCORE: " + this.score;
            document.getElementById("overlay").style.display = "flex";
        }
    },

    collide() {
        const [m, o] = [this.piece.matrix, this.piece.pos];
        for (let y = 0; y < m.length; ++y) {
            for (let x = 0; x < m[y].length; ++x) {
                if (m[y][x] !== 0) {
                    if (!this.grid[y + o.y] || this.grid[y + o.y][x + o.x] !== 0) return true;
                }
            }
        }
        return false;
    },

    merge() {
        this.piece.matrix.forEach((row, y) => {
            row.forEach((value, x) => {
                if (value) this.grid[y + this.piece.pos.y][x + this.piece.pos.x] = this.piece.color;
            });
        });
    },

    sweep() {
        let lines = 0;
        outer: for (let y = ROWS - 1; y >= 0; --y) {
            for (let x = 0; x < COLS; ++x) {
                if (this.grid[y][x] === 0) continue outer;
            }
            this.grid.splice(y, 1);
            this.grid.unshift(Array(COLS).fill(0));
            ++y;
            lines++;
        }
        if (lines > 0) {
            this.score += lines * 100;
            document.getElementById("score").innerText = this.score;
            this.dropInterval = Math.max(100, 800 - (Math.floor(this.score / 500) * 50));
        }
    },

    draw() {
        // 背景クリア
        this.ctx.fillStyle = "#000";
        this.ctx.fillRect(0, 0, COLS, ROWS);

        // 背景に薄いグリッドを描画
        this.ctx.strokeStyle = "#111";
        this.ctx.lineWidth = 0.02;
        for(let i=0; i<=COLS; i++) { this.ctx.moveTo(i,0); this.ctx.lineTo(i,ROWS); }
        for(let i=0; i<=ROWS; i++) { this.ctx.moveTo(0,i); this.ctx.lineTo(COLS,i); }
        this.ctx.stroke();

        // ブロック描画の共通設定
        this.ctx.shadowBlur = 10; // ネオンの光り具合

        // 固定済み
        this.grid.forEach((row, y) => {
            row.forEach((color, x) => {
                if (color) this.drawBlock(x, y, color);
            });
        });

        // 操作中
        if(this.piece) {
            this.piece.matrix.forEach((row, y) => {
                row.forEach((value, x) => {
                    if (value) this.drawBlock(x + this.piece.pos.x, y + this.piece.pos.y, this.piece.color);
                });
            });
        }
    },

    drawBlock(x, y, color) {
        this.ctx.shadowColor = color;
        this.ctx.fillStyle = color;
        // 中を少しだけ透過させて「管」のような見た目にする
        this.ctx.globalAlpha = 0.8;
        this.ctx.fillRect(x + 0.05, y + 0.05, 0.9, 0.9);
        this.ctx.globalAlpha = 1.0;
        
        // 縁を白っぽくして発光感を強調
        this.ctx.strokeStyle = "#fff";
        this.ctx.lineWidth = 0.05;
        this.ctx.strokeRect(x + 0.1, y + 0.1, 0.8, 0.8);
    },

    update(time = 0) {
        if (this.over) return;
        const deltaTime = time - this.lastTime;
        this.lastTime = time;
        this.dropCounter += deltaTime;
        if (this.dropCounter > this.dropInterval) {
            this.drop();
        }
        this.draw();
        requestAnimationFrame(this.update.bind(this));
    },

    drop() {
        this.piece.pos.y++;
        if (this.collide()) {
            this.piece.pos.y--;
            this.merge();
            this.sweep();
            this.spawn();
        }
        this.dropCounter = 0;
    },

    rotate() {
        const m = this.piece.matrix;
        const rotated = m[0].map((_, i) => m.map(row => row[i]).reverse());
        const prevMatrix = this.piece.matrix;
        this.piece.matrix = rotated;
        if (this.collide()) this.piece.matrix = prevMatrix;
    }
};

document.addEventListener("keydown", e => {
    if (Game.over) return;
    if (e.key === "ArrowLeft") { Game.piece.pos.x--; if(Game.collide()) Game.piece.pos.x++; }
    if (e.key === "ArrowRight") { Game.piece.pos.x++; if(Game.collide()) Game.piece.pos.x--; }
    if (e.key === "ArrowDown") { Game.drop(); }
    if (e.key === "ArrowUp") { Game.rotate(); }
    if (e.key === " ") { 
        while(!Game.collide()){ Game.piece.pos.y++; } 
        Game.piece.pos.y--; 
        Game.drop(); 
        e.preventDefault(); 
    }
});

Game.init();
</script>
</body>
</html>
