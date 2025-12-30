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
.stop{background:#e74c3c;color:#fff}
.play{background:#2ecc71;color:#000}
.reset{background:#f1c40f;color:#000}

.bell{font-size:22px;cursor:pointer}
.bell.notify{color:#0ff}

.card{
  background:#15151c;
  margin:30px auto;
  padding:20px;
  width:90%;
  max-width:420px;
  border-radius:10px;
}

/* パスワード */
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
    <button class="reserve" id="reserveBtn" onclick="reserve()">予約する</button>
    <button class="admin" onclick="openPassword()">管理者パネル</button>
    <span id="userBell" class="bell">🔔</span>
  </div>
  <div id="userMsg"></div>
</header>

<!-- 管理者画面 -->
<header id="adminView" class="hidden">
  <h1>スペレンタル予約 <span class="bell" onclick="toggleNotify()">🔔</span></h1>
</header>

<!-- 管理者パネル -->
<div id="adminPanel" class="card hidden">
  <button class="stop" onclick="stopReserve()">予約停止</button>
  <button class="play" onclick="resumeReserve()">予約再生</button>
  <button class="reset" onclick="resetReserve()">再予約可能</button>

  <hr><h3>チケット</h3>
  <input id="ticketName" placeholder="名前">
  <input id="ticketCount" type="number" placeholder="回数">
  <button onclick="addTicket()">追加</button>
  <div id="tickets"></div>
</div>

<!-- 通知 -->
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
let state = JSON.parse(localStorage.getItem("state")) || {
  stopped:false,
  reservation:null,
  notify:false,
  tickets:[]
};

function save(){ localStorage.setItem("state",JSON.stringify(state)); updateUI(); }

function reserve(){
  if(state.stopped){ alert("予約停止中"); return; }
  const name = prompt("ニックネーム");
  if(!name) return;
  state.reservation = name;
  state.notify = true;
  save();
}

function updateUI(){
  document.getElementById("reserveBtn").disabled = state.stopped || state.reservation;
  document.getElementById("userMsg").innerText =
    state.stopped ? "予約停止中" : "";
  document.getElementById("userBell").classList.toggle("notify",state.notify);

  const t = document.getElementById("tickets");
  t.innerHTML="";
  state.tickets.forEach((tk,i)=>{
    t.innerHTML+=`${tk.name} ${tk.count} <button onclick="delTicket(${i})">🗑️</button><br>`;
  });
}

function openPassword(){
  document.getElementById("passwordBox").classList.remove("hidden");
}

function add(n){
  const i=document.getElementById("passInput");
  if(i.value.length>=5) return;
  i.value+=n;
  if(i.value.length===5) check();
}
function clearPass(){ document.getElementById("passInput").value=""; }

function check(){
  const i=document.getElementById("passInput");
  if(i.value==="36994"){
    passwordBox.classList.add("hidden");
    userView.classList.add("hidden");
    adminView.classList.remove("hidden");
    adminPanel.classList.remove("hidden");
  }else{ alert("違います"); clearPass(); }
}

function toggleNotify(){
  const n=document.getElementById("notify");
  if(!state.reservation){
    n.innerHTML="通知なし";
  }else{
    n.innerHTML=`${state.reservation} が予約しました<br><br>
    <button onclick="approve()">承認</button>
    <button onclick="reject()">拒否</button>`;
  }
  n.classList.toggle("hidden");
}

function approve(){ state.reservation=null; state.notify=false; save(); }
function reject(){ state.reservation=null; state.notify=true; save(); }

function stopReserve(){ state.stopped=true; save(); }
function resumeReserve(){ state.stopped=false; save(); }
function resetReserve(){ state.reservation=null; save(); }

function addTicket(){
  const n=ticketName.value;
  const c=Number(ticketCount.value);
  if(!n||c<=0) return;
  state.tickets.push({name:n,count:c});
  save();
}
function delTicket(i){ state.tickets.splice(i,1); save(); }

updateUI();
</script>

</body>
</html>
