<!DOCTYPE html>
<html lang="ja">
<head>
<meta charset="UTF-8">
<title>スペレンタル予約</title>

<script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-app-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-database-compat.js"></script>

<style>
body{margin:0;background:#0b0b0e;color:#eaeaf0;font-family:Segoe UI}
header{display:flex;justify-content:space-between;align-items:center;padding:16px 24px;border-bottom:1px solid #222}
.card{background:#15151c;margin:30px auto;padding:24px;width:90%;max-width:520px;border-radius:8px}
.button-row{display:flex;justify-content:center;gap:24px;margin-top:20px}
button{padding:14px;font-size:16px;font-weight:600;border:none;border-radius:6px;cursor:pointer}
.approve{background:#2ecc71;color:#000}
.reject{background:#e74c3c;color:#000}
.disabled{background:#444;color:#777;cursor:not-allowed}
.hidden{display:none}
.bell{font-size:22px;cursor:pointer}
.neon{color:#00f6ff;text-shadow:0 0 10px #00f6ff}
.alert{color:#ff3b3b;margin-top:8px}
.user{display:flex;justify-content:space-between;align-items:center;margin:8px 0}
.menu{cursor:pointer}
.close{cursor:pointer}
.keypad{display:grid;grid-template-columns:repeat(3,1fr);gap:10px;margin-top:16px}
input[type=password]{width:100%;padding:10px;font-size:20px;text-align:center}
</style>
</head>

<body>

<header>
  <div>スペレンタル予約</div>
  <div id="bell" class="bell">🔔</div>
</header>

<div id="nameCard" class="card hidden">
  <h3>ニックネームを決めてください</h3>
  <input id="nameInput">
  <br><br>
  <button onclick="saveName()">決定</button>
</div>

<div id="mainCard" class="card hidden">
  <div id="status"></div>
  <div class="button-row">
    <button id="okBtn" class="approve">⭕ 予約</button>
    <button id="ngBtn" class="reject">❌</button>
  </div>
  <div id="alert" class="alert hidden">▲ 通知が来ています！</div>
</div>

<div id="notifyCard" class="card hidden"></div>

<div id="adminCard" class="card hidden">
  <div style="display:flex;justify-content:space-between;align-items:center">
    <h3>管理者パネル</h3>
    <div class="close" onclick="openKeypad()">❌</div>
  </div>
  <div id="users"></div>
</div>

<div id="passwordCard" class="card hidden">
  <h3>パスワード入力</h3>
  <input id="pwDisplay" type="password" disabled autocomplete="off">
  <div class="keypad">
    <button onclick="addNum(1)">1</button>
    <button onclick="addNum(2)">2</button>
    <button onclick="addNum(3)">3</button>
    <button onclick="addNum(4)">4</button>
    <button onclick="addNum(5)">5</button>
    <button onclick="addNum(6)">6</button>
    <button onclick="addNum(7)">7</button>
    <button onclick="addNum(8)">8</button>
    <button onclick="addNum(9)">9</button>
    <button onclick="clearPw()">C</button>
    <button onclick="addNum(0)">0</button>
    <button onclick="checkPw()">OK</button>
  </div>
</div>

<script>
firebase.initializeApp({
  apiKey:"YOUR_KEY",
  authDomain:"YOUR_DOMAIN",
  databaseURL:"YOUR_DB_URL",
  projectId:"YOUR_ID"
});
const db=firebase.database();

const ADMIN="SPEONS🛡️";
const ADMIN_PIN="369963";

let nickname=localStorage.getItem("nickname");
const isAdmin=nickname===ADMIN;
let pwInput="";

const status=document.getElementById("status");
const okBtn=document.getElementById("okBtn");
const ngBtn=document.getElementById("ngBtn");
const alertBox=document.getElementById("alert");
const adminCard=document.getElementById("adminCard");
const passwordCard=document.getElementById("passwordCard");

if(!nickname){
  nameCard.classList.remove("hidden");
}else{
  mainCard.classList.remove("hidden");
  db.ref("users/"+nickname).set({stopped:false});
  if(isAdmin) adminCard.classList.remove("hidden");
}

function saveName(){
  const v=nameInput.value;
  if(!v)return;
  localStorage.setItem("nickname",v);
  location.reload();
}

okBtn.onclick=ngBtn.onclick=()=>{
  db.ref("reservation").set({user:nickname,status:"pending"});
};

db.ref("users/"+nickname+"/stopped").on("value",s=>{
  if(s.val()){
    status.textContent="あなたの予約は停止されています";
    okBtn.disabled=ngBtn.disabled=true;
    okBtn.classList.add("disabled");
    ngBtn.classList.add("disabled");
  }else{
    status.textContent="";
    okBtn.disabled=ngBtn.disabled=false;
    okBtn.classList.remove("disabled");
    ngBtn.classList.remove("disabled");
  }
});

if(isAdmin){
  db.ref("users").on("value",snap=>{
    const users=snap.val()||{};
    usersDiv=document.getElementById("users");
    usersDiv.innerHTML="";
    Object.keys(users).forEach(u=>{
      usersDiv.innerHTML+=`
        <div class="user">
          <span>${u}</span>
          <span class="menu" onclick="toggleUser('${u}')">︙</span>
        </div>
      `;
    });
  });
}

function toggleUser(u){
  db.ref("users/"+u+"/stopped").once("value").then(s=>{
    db.ref("users/"+u+"/stopped").set(!s.val());
  });
}

function openKeypad(){
  pwInput="";
  pwDisplay.value="";
  passwordCard.classList.remove("hidden");
}

function addNum(n){
  if(pwInput.length>=6)return;
  pwInput+=n;
  pwDisplay.value=pwInput;
}

function clearPw(){
  pwInput="";
  pwDisplay.value="";
}

function checkPw(){
  if(pwInput===ADMIN_PIN){
    adminCard.classList.add("hidden");
    passwordCard.classList.add("hidden");
    clearPw();
  }
}
</script>

</body>
</html>
