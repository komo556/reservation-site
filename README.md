<!DOCTYPE html>
<html lang="ja">
<head>
<meta charset="UTF-8">
<title>スペレンタル予約</title>

<!-- Firebase -->
<script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-app-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-database-compat.js"></script>

<style>
body {
  margin:0;
  background:#0b0b0e;
  color:#eaeaf0;
  font-family:Segoe UI,sans-serif;
}

header {
  display:flex;
  justify-content:space-between;
  align-items:center;
  padding:16px 24px;
  border-bottom:1px solid #222;
}

.card {
  background:#15151c;
  margin:30px auto;
  padding:24px;
  width:90%;
  max-width:500px;
  border-radius:8px;
  text-align:center;
}

.button-row {
  display:flex;
  justify-content:center;
  gap:24px;
  margin-top:20px;
}

button {
  width:200px;
  padding:14px;
  font-size:16px;
  font-weight:600;
  border:none;
  border-radius:6px;
  cursor:pointer;
}

.approve { background:#2ecc71; color:#000; }
.reject  { background:#e74c3c; color:#000; }
.disabled { background:#444; color:#777; cursor:not-allowed; }

.bell {
  font-size:22px;
  cursor:pointer;
}

.neon {
  color:#00f6ff;
  box-shadow:0 0 12px #00f6ff;
}

.alert {
  color:#ff3b3b;
  margin-top:8px;
  font-size:14px;
}

.hidden { display:none; }
</style>
</head>
<body>

<header>
  <div>スペレンタル予約</div>
  <div id="bell" class="bell">🔔</div>
</header>

<div id="nameCard" class="card hidden">
  <h3>ニックネームを決めてください</h3>
  <input id="nameInput" placeholder="ニックネーム">
  <br><br>
  <button onclick="saveName()">決定</button>
</div>

<div id="mainCard" class="card hidden">
  <div id="status"></div>

  <div class="button-row">
    <button id="okBtn" class="approve">⭕ 予約</button>
    <button id="ngBtn" class="reject">❌ キャンセル</button>
  </div>

  <div id="alert" class="alert hidden">
    ▲ 通知が来ています！
  </div>
</div>

<div id="notifyCard" class="card hidden"></div>

<script>
/* Firebase 設定（差し替え） */
const firebaseConfig = {
  apiKey: "YOUR_KEY",
  authDomain: "YOUR_DOMAIN",
  databaseURL: "YOUR_DB_URL",
  projectId: "YOUR_ID"
};
firebase.initializeApp(firebaseConfig);
const db = firebase.database();

/* 状態 */
let nickname = localStorage.getItem("nickname");
const isAdmin = nickname === "管理者"; // 管理者名を決めておく

const bell = document.getElementById("bell");
const notifyCard = document.getElementById("notifyCard");
const alertBox = document.getElementById("alert");

/* 初期表示 */
if (!nickname) {
  document.getElementById("nameCard").classList.remove("hidden");
} else {
  document.getElementById("mainCard").classList.remove("hidden");
}

/* 名前保存 */
function saveName() {
  nickname = document.getElementById("nameInput").value;
  if (!nickname) return;
  localStorage.setItem("nickname", nickname);
  location.reload();
}

/* 予約 */
document.getElementById("okBtn").onclick =
document.getElementById("ngBtn").onclick = () => {
  db.ref("reservation").set({
    user: nickname,
    status: "pending"
  });
};

/* 通知監視 */
db.ref("reservation").on("value", snap => {
  const data = snap.val();
  if (!data) return;

  // 管理者側
  if (isAdmin && data.status === "pending") {
    showAdminNotify(data.user);
  }

  // 一般ユーザー側（拒否）
  if (!isAdmin && data.status === "rejected" && data.user === nickname) {
    bell.classList.add("neon");
    alertBox.classList.remove("hidden");
  }
});

/* 管理者通知 */
function showAdminNotify(user) {
  bell.classList.add("neon");
  notifyCard.classList.remove("hidden");
  notifyCard.innerHTML = `
    <h3>${user} が予約しました</h3>
    <button onclick="approve()">承認</button>
    <button onclick="reject()">拒否</button>
  `;
}

function approve() {
  db.ref("reservation/status").set("approved");
  clearNotify();
}

function reject() {
  db.ref("reservation/status").set("rejected");
  clearNotify();
}

function clearNotify() {
  bell.classList.remove("neon");
  notifyCard.classList.add("hidden");
}

/* 🔔 クリック */
bell.onclick = () => {
  notifyCard.classList.toggle("hidden");
};
</script>

</body>
</html>
