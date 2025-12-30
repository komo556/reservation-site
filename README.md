<!DOCTYPE html>
<html lang="ja">
<head>
<meta charset="UTF-8">
<title>スペレンタル予約</title>
<style>
body { font-family: sans-serif; text-align:center; }
button { padding:10px 20px; margin:5px; font-size:16px; }
.green { background:#4CAF50; color:white; }
.red { background:#f44336; color:white; }
.gray { background:#aaa; color:white; }
#status { margin:15px; font-weight:bold; }
#menu { position:absolute; top:10px; left:10px; cursor:pointer; font-size:24px; }
#admin { display:none; border:1px solid #ccc; padding:10px; margin-top:20px; }
.ticket { border:1px solid #ccc; margin:5px; padding:5px; }
</style>
</head>
<body>

<div id="menu">☰</div>

<h2>スペレンタル予約</h2>
<div id="status"></div>

<button id="okBtn" class="green">⭕</button>
<button id="ngBtn" class="red">❌</button>

<h3>チケット</h3>
<div id="tickets"></div>

<div id="admin">
<h3>管理者パネル</h3>
<button onclick="resetReservation()">再予約可能</button>
<button onclick="stopReservation()">予約停止</button>
<button onclick="resumeReservation()">予約再生</button>

<h4>チケット追加</h4>
<input id="tName" placeholder="名前">
<input id="tCount" type="number" placeholder="回数">
<button onclick="addTicket()">追加</button>

<h4>チケット管理</h4>
<div id="adminTickets"></div>
<button onclick="deleteAllTickets()">全て削除</button>
</div>

<script>
let state = JSON.parse(localStorage.getItem("state")) || {
  reserved:false,
  stopped:false,
  tickets:[]
};

const okBtn = document.getElementById("okBtn");
const ngBtn = document.getElementById("ngBtn");
const status = document.getElementById("status");

function save(){ localStorage.setItem("state", JSON.stringify(state)); render(); }

function reserve(){
  if(state.reserved || state.stopped) return;
  status.textContent="予約中…";
  setTimeout(()=>{
    status.textContent="予約完了";
    state.reserved=true;
    save();
  },2000);
}

okBtn.onclick = reserve;
ngBtn.onclick = reserve;

function render(){
  if(state.stopped){
    status.textContent="予約停止中、管理者に相談してください";
    okBtn.disabled = ngBtn.disabled = true;
    okBtn.className = ngBtn.className = "gray";
  } else if(state.reserved){
    okBtn.disabled = ngBtn.disabled = true;
    okBtn.className = ngBtn.className = "gray";
  } else {
    okBtn.disabled = ngBtn.disabled = false;
    okBtn.className="green";
    ngBtn.className="red";
  }

  const tDiv = document.getElementById("tickets");
  const aDiv = document.getElementById("adminTickets");
  tDiv.innerHTML = aDiv.innerHTML = "";

  state.tickets.forEach((t,i)=>{
    if(t.count>0){
      const d=document.createElement("div");
      d.className="ticket";
      d.textContent=`${t.name} ${t.count}`;
      d.onclick=()=>{ t.count--; save(); };
      tDiv.appendChild(d);
    }

    const ad=document.createElement("div");
    ad.innerHTML=`${t.name} ${t.count} <button onclick="deleteTicket(${i})">🗑️</button>`;
    aDiv.appendChild(ad);
  });
}

function resetReservation(){ state.reserved=false; save(); }
function stopReservation(){ state.stopped=true; save(); }
function resumeReservation(){ state.stopped=false; save(); }

function addTicket(){
  const n=document.getElementById("tName").value;
  const c=Number(document.getElementById("tCount").value);
  if(n && c>0){
    state.tickets.push({name:n,count:c});
    save();
  }
}

function deleteTicket(i){ state.tickets.splice(i,1); save(); }
function deleteAllTickets(){ state.tickets=[]; save(); }

document.getElementById("menu").onclick=()=>{
  const p=prompt("パスワード");
  if(p==="36bbmd") document.getElementById("admin").style.display="block";
};

render();
</script>

</body>
</html>
