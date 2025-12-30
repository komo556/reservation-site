<!DOCTYPE html>
<html lang="ja">
<head>
<meta charset="UTF-8">
<title>スペレンタル予約</title>
<style>
body {
  font-family: sans-serif;
  background: #f5f5f5;
  text-align: center;
}
button {
  padding: 10px 16px;
  margin: 5px;
  font-size: 16px;
}
.hidden { display: none; }

#adminPanel {
  background: #fff;
  padding: 15px;
  margin: 10px;
  border-radius: 10px;
  position: relative;
}
#closeBtn {
  position: absolute;
  left: 10px;
  top: 10px;
  font-size: 20px;
  cursor: pointer;
}

.keypad button {
  width: 60px;
  height: 50px;
  font-size: 18px;
}

.red { background: #ff6b6b; }
.green { background: #51cf66; }
.yellow { background: #ffd43b; }

table {
  margin: auto;
  border-collapse: collapse;
}
td, th {
  border: 1px solid #aaa;
  padding: 6px 10px;
}
</style>
</head>

<body>

<h2>利用者画面</h2>
<h1>スペレンタル予約</h1>

<button>予約する</button>
<button onclick="openKeypad()">管理者パネル</button>
<button>🔔</button>

<!-- キーパッド -->
<div id="keypad" class="hidden">
  <h3>パスワード入力</h3>
  <div id="passDisplay">●●●●●</div>

  <div class="keypad">
    <div>
      <button onclick="press(1)">1</button>
      <button onclick="press(2)">2</button>
      <button onclick="press(3)">3</button>
    </div>
    <div>
      <button onclick="press(4)">4</button>
      <button onclick="press(5)">5</button>
      <button onclick="press(6)">6</button>
    </div>
    <div>
      <button onclick="press(7)">7</button>
      <button onclick="press(8)">8</button>
      <button onclick="press(9)">9</button>
    </div>
    <div>
      <button style="visibility:hidden">x</button>
      <button onclick="press(0)">0</button>
      <button onclick="clearPass()">C</button>
    </div>
  </div>
</div>

<!-- 管理者パネル -->
<div id="adminPanel" class="hidden">
  <div id="closeBtn" onclick="closeAdmin()">❌</div>

  <h2>管理者パネル</h2>

  <button class="red">予約停止</button>
  <button class="green">予約再生</button>
  <button class="yellow">再予約可能</button>

  <h3>チケット</h3>

  <input id="nameInput" placeholder="名前">
  <input id="countInput" type="number" placeholder="回数">
  <button onclick="addTicket()">追加</button>

  <table id="ticketTable">
    <tr><th>名前</th><th>回数</th><th>削除</th></tr>
  </table>
</div>

<script>
let input = "";
const PASSWORD = "36994";

function openKeypad() {
  document.getElementById("keypad").classList.remove("hidden");
}

function press(num) {
  input += num;
  if (input.length === PASSWORD.length) {
    if (input === PASSWORD) {
      document.getElementById("keypad").classList.add("hidden");
      document.getElementById("adminPanel").classList.remove("hidden");
    }
    input = "";
  }
}

function clearPass() {
  input = "";
}

function closeAdmin() {
  document.getElementById("adminPanel").classList.add("hidden");
  openKeypad();
}

function addTicket() {
  const name = nameInput.value;
  const count = countInput.value;
  if (!name || !count) return;

  const row = ticketTable.insertRow();
  row.insertCell(0).innerText = name;
  row.insertCell(1).innerText = count;
  row.insertCell(2).innerHTML = "🗑️";
  row.cells[2].onclick = () => row.remove();

  nameInput.value = "";
  countInput.value = "";
}
</script>

</body>
</html>
