<!DOCTYPE html>
<html lang="ja">
<head>
<meta charset="UTF-8">
<title>予約管理</title>
<style>
body {
  margin: 0;
  background: #000;
  color: #fff;
  font-family: sans-serif;
}
.hidden { display: none; }

#app { padding: 20px; }

button {
  background: #222;
  color: #fff;
  border: 1px solid #555;
  padding: 6px 10px;
  cursor: pointer;
}
button.red { color: #ff5555; }
button.green { color: #55ff88; }

.notify {
  color: #ff0;
  text-shadow: 0 0 10px #ff0;
}

.panel {
  border: 1px solid #333;
  padding: 10px;
  margin-top: 10px;
}

.menu {
  cursor: pointer;
  padding: 0 8px;
}

.keypad button {
  width: 60px;
  height: 60px;
  margin: 4px;
  font-size: 20px;
}
</style>
</head>

<body>
<div id="app">

<!-- 名前入力 -->
<div id="nameScreen">
  <h2>ニックネームを決めてください</h2>
  <input id="nickname" autocomplete="off">
  <button onclick="enter()">決定</button>
</div>

<!-- メイン -->
<div id="main" class="hidden">
  <h2 id="title"></h2>
  <div>
    🔔 <span id="bell">通知なし</span>
  </div>

  <button onclick="reserve()">予約する</button>

  <div id="adminPanel" class="panel hidden">
    <div>
      <span>管理者パネル</span>
      <span style="float:right;cursor:pointer" onclick="closeAdmin()">❌</span>
    </div>
    <div id="notifications"></div>
  </div>

  <button onclick="openAdmin()">管理者パネル</button>
</div>

<!-- パスワード -->
<div id="passwordScreen" class="hidden panel">
  <h3>パスワード入力</h3>
  <div id="pwDisplay">●●●●</div>
  <div class="keypad">
    <button onclick="pw(1)">1</button>
    <button onclick="pw(2)">2</button>
    <button onclick="pw(3)">3</button><br>
    <button onclick="pw(4)">4</button>
    <button onclick="pw(5)">5</button>
    <button onclick="pw(6)">6</button><br>
    <button onclick="pw(7)">7</button>
    <button onclick="pw(8)">8</button>
    <button onclick="pw(9)">9</button><br>
    <button onclick="pwClear()">C</button>
    <button onclick="pw(0)">0</button>
  </div>
</div>

</div>

<script>
let user = "";
let isAdmin = false;
let stopped = false;
let ticket = true;
let notifications = [];
let pwInput = "";
const PASSWORD = "2580";

function enter() {
  user = nickname.value;
  if (!user) return;
  document.getElementById("nameScreen").classList.add("hidden");
  document.getElementById("main").classList.remove("hidden");
  title.innerText = user === "SPEONS🛡️" ? "管理者 SPEONS🛡️" : user;
  isAdmin = user === "SPEONS🛡️";
}

function reserve() {
  if (stopped || !ticket) return alert("予約できません");
  notifications.push(user + " が予約しました");
  bell.innerText = "▲ 通知あり";
  bell.classList.add("notify");
  renderNotifications();
}

function renderNotifications() {
  notificationsDiv = document.getElementById("notifications");
  notificationsDiv.innerHTML = "";
  notifications.forEach((n, i) => {
    notificationsDiv.innerHTML += `
      <div>
        ${n}
        <button onclick="approve(${i})">⭕</button>
        <button onclick="reject(${i})">❌</button>
      </div>`;
  });
}

function approve(i) {
  notifications.splice(i,1);
  renderNotifications();
}

function reject(i) {
  notifications.splice(i,1);
  bell.innerText = "▲ 通知が来ています！";
  renderNotifications();
}

function openAdmin() {
  if (!isAdmin) return;
  adminPanel.classList.remove("hidden");
}

function closeAdmin() {
  adminPanel.classList.add("hidden");
  passwordScreen.classList.remove("hidden");
}

function pw(n) {
  pwInput += n;
  if (pwInput.length === 4) {
    if (pwInput === PASSWORD) {
      passwordScreen.classList.add("hidden");
    }
    pwClear();
  }
}

function pwClear() {
  pwInput = "";
}
</script>
</body>
</html>
