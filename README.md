<!DOCTYPE html>
<html lang="ja">
<head>
<meta charset="UTF-8">
<title>スペレンタル予約</title>

<style>
body{
  margin:0;
  background:#0b0b0e;
  color:#eaeaf0;
  font-family:Segoe UI,sans-serif;
  text-align:center;
}
.hidden{display:none}
header{padding:30px}
h1{cursor:pointer}

.actions{
  display:flex;
  justify-content:center;
  gap:20px;
  margin-top:40px;
}

button{
  padding:14px 24px;
  font-size:16px;
  font-weight:700;
  border:none;
  border-radius:8px;
  cursor:pointer;
}

.reserve{background:#2ecc71;color:#000}
.admin{background:#2c2c36;color:#fff}
.bell{font-size:22px;cursor:pointer}

.card{
  background:#15151c;
  margin:30px auto;
  padding:20px;
  width:90%;
  max-width:360px;
  border-radius:10px;
}

/* パスワード表示 */
input{
  width:100%;
  padding:12px;
  font-size:20px;
  text-align:center;
  margin-bottom:20px;
  background:#000;
  color:#0ff;
  border:none;
}

/* キーパッド */
.keypad{
  display:grid;
  grid-template-columns:repeat(3,1fr);
  gap:12px;
}
.keypad button{
  height:60px;
  font-size:20px;
}
.empty{visibility:hidden}
</style>
</head>

<body>

<!-- 利用者画面 -->
<header id="userView">
  <h1 id="title">スペレンタル予約</h1>
  <div class="actions">
    <button class="reserve" onclick="reserve()">予約する</button>
    <button class="admin">管理者パネル</button>
    <span class="bell">🔔</span>
  </div>
</header>

<!-- 管理者画面 -->
<header id="adminView" class="hidden">
  <h1>スペレンタル予約 <span class="bell" onclick="toggleNotify()">🔔</span></h1>
</header>

<div id="notify" class="card hidden"></div>

<!-- パスワード -->
<div id="passwordBox" class="card hidden">
  <div>管理者パスワード</div>
  <input id="passInput" type="password" readonly>

  <div class="keypad">
    <button onclick="add('1')">1</button>
    <button onclick="add('2')">2</button>
    <button onclick="add('3')">3</button>

    <button onclick="add('4')">4</button>
    <button onclick="add('5')">5</button>
    <button onclick="add('6')">6</button>

    <button onclick="add('7')">7</button>
    <button onclick="add('8')">8</button>
    <button onclick="add('9')">9</button>

    <div class="empty"></div>
    <button onclick="add('0')">0</button>
    <button onclick="clearPass()">C</button>
  </div>
</div>

<script>
let reservationName = null;

// 予約（仮）
function reserve(){
  const name = prompt("名前を入力");
  if(!name) return;
  reservationName = name;
  alert("予約完了");
}

// タイトル → パスワード表示
document.getElementById("title").onclick = ()=>{
  document.getElementById("passwordBox").classList.remove("hidden");
};

// キーパッド処理
function add(n){
  const input = document.getElementById("passInput");
  if(input.value.length >= 5) return;
  input.value += n;
  if(input.value.length === 5) check();
}

function clearPass(){
  document.getElementById("passInput").value = "";
}

function check(){
  const input = document.getElementById("passInput");
  if(input.value === "36994"){
    document.getElementById("passwordBox").classList.add("hidden");
    document.getElementById("userView").classList.add("hidden");
    document.getElementById("adminView").classList.remove("hidden");
  }else{
    alert("パスワードが違います");
    clearPass();
  }
}

// 通知（仮）
function toggleNotify(){
  const n = document.getElementById("notify");
  if(!reservationName){
    n.innerHTML="予約はありません";
  }else{
    n.innerHTML=`${reservationName} が予約しました<br><br>
    <button onclick="approve()">承認</button>
    <button onclick="reject()">拒否</button>`;
  }
  n.classList.toggle("hidden");
}
function approve(){ reservationName=null; toggleNotify(); }
function reject(){ reservationName=null; toggleNotify(); }
</script>

</body>
</html>
