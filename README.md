<!DOCTYPE html>
<html lang="ja">
<head>
<meta charset="UTF-8">
<title>Advanced RPG</title>

<style>
  body {
    background: #0d0d0d;
    color: #fff;
    font-family: sans-serif;
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
  }

  .game {
    width: 400px;
    background: #1b1b1b;
    border-radius: 15px;
    padding: 15px;
  }

  .screen {
    display: none;
  }

  .active {
    display: block;
  }

  .map {
    display: grid;
    grid-template-columns: repeat(5, 1fr);
    gap: 5px;
    margin: 10px 0;
  }

  .cell {
    height: 50px;
    background: #333;
    display: flex;
    justify-content: center;
    align-items: center;
    border-radius: 8px;
  }

  .player {
    background: #00ffc6;
    color: #000;
  }

  button {
    width: 100%;
    padding: 10px;
    margin-top: 5px;
    border: none;
    border-radius: 10px;
    cursor: pointer;
  }

  .log {
    background: #000;
    height: 80px;
    overflow-y: auto;
    font-size: 13px;
    padding: 5px;
  }
</style>
</head>

<body>

<div class="game">

  <div id="status">
    Lv:<span id="lv">1</span>
    HP:<span id="hp">100</span>
    EXP:<span id="exp">0</span>
  </div>

  <!-- MAP SCREEN -->
  <div id="mapScreen" class="screen active">
    <h3>🗺️ フィールド</h3>
    <div class="map" id="map"></div>
    <button onclick="move()">進む</button>
  </div>

  <!-- BATTLE SCREEN -->
  <div id="battleScreen" class="screen">
    <h3>⚔️ 戦闘</h3>
    敵HP: <span id="enemyHP"></span>
    <div class="log" id="battleLog"></div>
    <button onclick="attack()">攻撃</button>
    <button onclick="usePotion()">回復薬</button>
  </div>

</div>

<script>
  // プレイヤー
  let player = {
    lv: 1,
    hp: 100,
    maxHp: 100,
    exp: 0,
    potions: 3,
    x: 2,
    y: 2
  };

  let enemy = null;

  const mapEl = document.getElementById("map");
  const logEl = document.getElementById("battleLog");

  function drawMap() {
    mapEl.innerHTML = "";
    for (let y = 0; y < 5; y++) {
      for (let x = 0; x < 5; x++) {
        const cell = document.createElement("div");
        cell.className = "cell";
        if (player.x === x && player.y === y) {
          cell.classList.add("player");
          cell.textContent = "勇";
        }
        mapEl.appendChild(cell);
      }
    }
  }

  function move() {
    const dir = Math.floor(Math.random() * 4);
    if (dir === 0 && player.x > 0) player.x--;
    if (dir === 1 && player.x < 4) player.x++;
    if (dir === 2 && player.y > 0) player.y--;
    if (dir === 3 && player.y < 4) player.y++;

    drawMap();

    if (Math.random() < 0.4) startBattle();
  }

  function startBattle() {
    enemy = {
      hp: 30 + player.lv * 10
    };
    switchScreen("battleScreen");
    document.getElementById("enemyHP").textContent = enemy.hp;
    logEl.innerHTML = "敵が現れた！<br>";
  }

  function attack() {
    const dmg = Math.floor(Math.random() * 10) + 5;
    enemy.hp -= dmg;
    log(`攻撃！${dmg}ダメージ`);

    if (enemy.hp <= 0) {
      winBattle();
      return;
    }

    enemyAttack();
  }

  function enemyAttack() {
    const dmg = Math.floor(Math.random() * 8) + 3;
    player.hp -= dmg;
    log(`敵の攻撃！${dmg}ダメージ`);

    if (player.hp <= 0) {
      alert("GAME OVER");
      location.reload();
    }

    updateStatus();
  }

  function usePotion() {
    if (player.potions <= 0) return;
    player.hp += 30;
    if (player.hp > player.maxHp) player.hp = player.maxHp;
    player.potions--;
    log("回復薬を使った");
    enemyAttack();
  }

  function winBattle() {
    const gain = 20;
    player.exp += gain;
    log(`勝利！EXP +${gain}`);
    if (player.exp >= 100) levelUp();
    switchScreen("mapScreen");
    updateStatus();
  }

  function levelUp() {
    player.lv++;
    player.exp = 0;
    player.maxHp += 20;
    player.hp = player.maxHp;
    alert("レベルアップ！");
  }

  function log(text) {
    logEl.innerHTML += text + "<br>";
    document.getElementById("enemyHP").textContent = enemy.hp;
  }

  function updateStatus() {
    document.getElementById("lv").textContent = player.lv;
    document.getElementById("hp").textContent = player.hp;
    document.getElementById("exp").textContent = player.exp;
  }

  function switchScreen(id) {
    document.querySelectorAll(".screen").forEach(s => s.classList.remove("active"));
    document.getElementById(id).classList.add("active");
  }

  drawMap();
  updateStatus();
</script>

</body>
</html>
