
<html lang="en">
<head>
<meta charset="UTF-8">
<title>Forja TV – Smart TV Pro</title>

<script src="https://cdn.jsdelivr.net/npm/hls.js@latest"></script>
<link href="https://fonts.googleapis.com/css2?family=Roboto:wght@400;700;900&family=Cairo:wght@400;700;900&display=swap" rel="stylesheet">

<style>
:root{
  --gold:#ffdd57;
  --bg:#0b0b0f;
  --glass:rgba(255,255,255,0.08);
  --focus:#00e5ff;
}

*{box-sizing:border-box}

body{
  margin:0;
  font-family:'Roboto',sans-serif;
  background:radial-gradient(circle at top,#1b1b2f,#0b0b0f);
  color:#fff;
}

body.rtl{
  direction:rtl;
  font-family:'Cairo',sans-serif;
}

/* TV focus */
.tv-focus{
  box-shadow:0 0 0 4px var(--focus);
  transform:scale(1.06);
}

/* Pages */
.page{
  min-height:100vh;
  padding:24px;
  display:flex;
  flex-direction:column;
  align-items:center;
}
.hidden{display:none}

/* Welcome */
#welcomeScreen{
  justify-content:center;
  text-align:center;
}
.header{
  font-size:3em;
  font-weight:900;
  color:var(--gold);
  margin-bottom:20px;
}
#welcomeText{
  max-width:800px;
  opacity:.9;
  margin-bottom:30px;
  line-height:1.4;
}

/* Controls */
button,select{
  padding:14px 26px;
  border-radius:14px;
  border:none;
  font-weight:700;
  cursor:pointer;
}
button{
  background:linear-gradient(145deg,#ffe97a,#ffcc00);
}

/* Top bar */
.topbar{
  width:100%;
  max-width:1300px;
  display:flex;
  justify-content:space-between;
  align-items:center;
  margin-bottom:16px;
}

/* Player */
#player-container{
  width:100%;
  max-width:1300px;
  background:rgba(0,0,0,.7);
  border-radius:22px;
  padding:12px;
  margin-bottom:20px;
  display:none;
  position:relative;
}
video{
  width:100%;
  border-radius:18px;
}
#live{
  position:absolute;
  top:16px;
  left:16px;
  background:red;
  padding:6px 14px;
  border-radius:8px;
  font-weight:700;
}

/* Tabs */
.tabs{
  width:100%;
  max-width:1300px;
  display:flex;
  gap:14px;
  overflow-x:auto;
  margin-bottom:18px;
}
.tab{
  padding:14px 26px;
  border-radius:18px;
  background:var(--glass);
  cursor:pointer;
  font-size:1.1em;
}
.tab.active{
  background:linear-gradient(145deg,#ffe97a,#ffcc00);
  color:#000;
}

/* Channels */
.channels-container{
  width:100%;
  max-width:1300px;
  display:grid;
  grid-template-columns:repeat(auto-fill,minmax(170px,1fr));
  gap:20px;
}
.card{
  background:linear-gradient(160deg,#1e1e2f,#0e0e14);
  border-radius:20px;
  padding:16px;
  cursor:pointer;
  display:flex;
  flex-direction:column;
  align-items:center;
  transition:.25s;
}
.card img{
  width:100%;
  height:90px;
  object-fit:contain;
  margin-bottom:12px;
}
.card span{
  font-size:.95em;
  text-align:center;
}

/* Error */
#error{
  color:#ff4c4c;
  margin-top:10px;
  display:none;
}
</style>
</head>

<body>

<!-- Welcome -->
<div class="page" id="welcomeScreen">
  <div class="header">Forja TV</div>

  <select id="langSelect">
    <option value="en">English</option>
    <option value="ar">العربية</option>
  </select>

  <div id="welcomeText"></div>
  <button id="watchNow"></button>
</div>

<!-- Main -->
<div class="page hidden" id="mainPage">

  <div class="topbar">
    <div class="header" style="font-size:1.6em">Forja TV</div>
  </div>

  <div id="player-container">
    <div id="live">LIVE</div>
    <video id="video" controls></video>
    <div id="error"></div>
  </div>

  <div class="tabs">
    <div class="tab active" data-cat="news" tabindex="0">📰 News</div>
    <div class="tab" data-cat="sports" tabindex="0">⚽ Sports</div>
    <div class="tab" data-cat="ent" tabindex="0">🎬 Entertainment</div>
    <div class="tab" data-cat="arabic" tabindex="0">🌍 Arabic</div>
  </div>

  <div class="channels-container" id="channels"></div>

</div>

<script>
/* TRANSLATIONS */
const T={
 en:{
  welcome:"A premium streaming experience where you can watch anything, anytime, from anywhere.",
  watch:"WATCH NOW",
  error:"Stream unavailable"
 },
 ar:{
  welcome:"تجربة بث احترافية تتيح لك مشاهدة أي محتوى، في أي وقت، ومن أي مكان.",
  watch:"شاهد الآن",
  error:"القناة غير متوفرة حالياً"
 }
};

let lang="en";

/* Elements */
const langSelect=document.getElementById("langSelect");
const welcomeText=document.getElementById("welcomeText");
const watchNow=document.getElementById("watchNow");
const errorBox=document.getElementById("error");

function applyLang(){
 document.body.classList.toggle("rtl",lang==="ar");
 welcomeText.textContent=T[lang].welcome;
 watchNow.textContent=T[lang].watch;
 errorBox.textContent=T[lang].error;
}
langSelect.onchange=()=>{lang=langSelect.value;applyLang();}
applyLang();

/* App logic */
const welcome=document.getElementById("welcomeScreen");
const main=document.getElementById("mainPage");
const channels=document.getElementById("channels");
const video=document.getElementById("video");
const player=document.getElementById("player-container");

let hls;

const playlists={
 news:"https://iptv-org.github.io/iptv/categories/news.m3u",
 sports:"https://iptv-org.github.io/iptv/categories/sports.m3u",
 ent:"https://iptv-org.github.io/iptv/categories/entertainment.m3u",
 arabic:"https://iptv-org.github.io/iptv/languages/ara.m3u"
};

watchNow.onclick=()=>{
 welcome.classList.add("hidden");
 main.classList.remove("hidden");
 load("news");
};

/* Parse M3U (logos) */
function parseM3U(txt){
 const lines=txt.split("\n");
 let list=[];
 for(let i=0;i<lines.length;i++){
  if(lines[i].startsWith("#EXTINF")){
   const name=lines[i].split(",")[1];
   const logo=(lines[i].match(/tvg-logo="([^"]+)"/)||[])[1]||"";
   list.push({name,logo,url:lines[i+1]});
  }
 }
 return list;
}

async function load(cat){
 channels.innerHTML="Loading...";
 const r=await fetch(playlists[cat]);
 const t=await r.text();
 const list=parseM3U(t);
 channels.innerHTML="";
 list.forEach(c=>{
  const d=document.createElement("div");
  d.className="card";
  d.tabIndex=0;
  d.innerHTML=`
    <img src="${c.logo}" onerror="this.style.display='none'">
    <span>${c.name}</span>
  `;
  d.onclick=()=>play(c.url);
  d.onfocus=()=>d.scrollIntoView({block:"center"});
  channels.appendChild(d);
 });
 setTVFocus();
}

function play(url){
 player.style.display="block";
 errorBox.style.display="none";
 if(hls)hls.destroy();
 if(video.canPlayType("application/vnd.apple.mpegurl")){
  video.src=url;
 }else{
  hls=new Hls();
  hls.loadSource(url);
  hls.attachMedia(video);
  hls.on(Hls.Events.ERROR,()=>errorBox.style.display="block");
 }
 video.play().catch(()=>errorBox.style.display="block");
}

/* Android TV navigation */
function setTVFocus(){
 const items=[...document.querySelectorAll("[tabindex='0']")];
 let i=0;
 items[i]?.classList.add("tv-focus");
 items[i]?.focus();

 document.onkeydown=e=>{
  items[i]?.classList.remove("tv-focus");
  if(e.key==="ArrowRight") i=Math.min(i+1,items.length-1);
  if(e.key==="ArrowLeft") i=Math.max(i-1,0);
  if(e.key==="ArrowDown") i=Math.min(i+4,items.length-1);
  if(e.key==="ArrowUp") i=Math.max(i-4,0);
  if(e.key==="Enter") items[i]?.click();
  items[i]?.classList.add("tv-focus");
  items[i]?.focus();
 };
}

/* Tabs */
document.querySelectorAll(".tab").forEach(tab=>{
 tab.onclick=()=>{
  document.querySelectorAll(".tab").forEach(t=>t.classList.remove("active"));
  tab.classList.add("active");
  load(tab.dataset.cat);
 };
});
</script>

</body>
</html>
