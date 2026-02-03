<!DOCTYPE html>
<html lang="ja">
<head>
<meta charset="UTF-8">
<title>Mini RPG</title>

<style>
  body {
    background: #111;
    color: #fff;
    font-family: sans-serif;
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
  }

  .game {
    width: 380px;
    background: #1e1e1e;
    border-radius: 15px;
    padding: 20px;
    box-shadow: 0 0 20px rgba(0,255,200,0.2);
  }

  h1 {
    text-align: center;
  }

  .status {
    display: flex;
    justify-content: space-between;
    margin: 15px 0;
  }

  .log {
    background: #000;
    padding: 10px;
    height: 100px;
    overflow-y: auto;
    font-size: 14px;
    margin-bottom: 10px;
  }

  button {
    width: 100%;
    padding: 10px;
    margin: 5px 0;
    font-size: 16px;
    border: none;
    border-radius: 10px;
    cursor: pointer;
  }

  .attack { background: #ff5555; }
  .heal { background: #55ff99; }
</style>
</head>

<body>

<div class="game">
  <h1>⚔️ Mini RPG</h1>

  <div class="status">
    <div>勇者 HP: <span id="playerHP">100</span></div>
    <div>魔物 HP: <span id="enemyHP">80</span></div>
  </div>

  <div class="log" id="log"></div>

  <button class="attack" onclick="attack()">攻撃</button>
  <button class="heal" onclick="heal()">回復</button>
</div>

<script>
  let playerHP = 100;
  let enemyHP = 80;

  const playerEl = document.getElementById("playerHP");
  const enemyEl = document.getElementById("enemyHP");
  const logEl = document.getElementById("log");

  function log(text) {
    logEl.innerHTML += text + "<br>";
    logEl.scrollTop = logEl.scrollHeight;
  }

  function update() {
    playerEl.textContent = playerHP;
    enemyEl.textContent = enemyHP;
  }

  function attack() {
    const damage = Math.floor(Math.random() * 15) + 5;
    enemyHP -= damage;
    log(`勇者の攻撃！ ${damage} ダメージ`);

    if (enemyHP <= 0) {
      enemyHP = 0;
      update();
      log("🎉 魔物を倒した！");
      endGame();
      return;
    }

    enemyTurn();
    update();
  }

  function heal() {
    const heal = Math.floor(Math.random() * 10) + 8;
    playerHP += heal;
    if (playerHP > 100) playerHP = 100;
    log(`勇者は ${heal} 回復した`);

    enemyTurn();
    update();
  }

  function enemyTurn() {
    const damage = Math.floor(Math.random() * 12) + 4;
    playerHP -= damage;
    log(`魔物の攻撃！ ${damage} ダメージ`);

    if (playerHP <= 0) {
      playerHP = 0;
      update();
      log("💀 勇者は倒れた…");
      endGame();
    }
  }

  function endGame() {
    document.querySelectorAll("button").forEach(btn => btn.disabled = true);
  }

  update();
</script>

</body>
</html>
