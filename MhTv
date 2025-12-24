<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Mohamed Amin TV Pro</title>
<link href="https://fonts.googleapis.com/css2?family=Roboto:wght@400;700&display=swap" rel="stylesheet">
<style>
  * { box-sizing: border-box; margin:0; padding:0; }
  body {
    font-family: 'Roboto', sans-serif;
    background-color: #0d0d0d;
    color: #fff;
    padding: 20px;
    scroll-behavior: smooth;
  }
  h1 {
    text-align: center;
    color: #ffcc00;
    font-size: 2.5em;
    margin-bottom: 10px;
  }
  #search-container {
    text-align: center;
    margin-bottom: 20px;
    position: sticky;
    top: 0;
    background: #0d0d0d;
    padding: 10px 0;
    z-index: 100;
  }
  #search-input {
    padding: 10px;
    width: 90%;
    max-width: 500px;
    border-radius: 8px;
    border: 2px solid #333;
    font-size: 1em;
    background: #1a1a1a;
    color: #fff;
  }
  #sections-nav {
    text-align:center;
    margin-bottom:20px;
  }
  .section-button {
    background:#333;
    color:#ffcc00;
    border:none;
    padding:8px 12px;
    margin:2px;
    border-radius:5px;
    cursor:pointer;
    transition:0.2s;
  }
  .section-button:hover { background:#ffcc00; color:#0d0d0d; }
  .video-container {
    display: flex;
    flex-direction: column;
    align-items: center;
    margin-bottom:40px;
  }
  video {
    width: 90%;
    max-width:900px;
    border:3px solid #ffcc00;
    border-radius:15px;
    background-color:black;
    box-shadow:0 0 20px #ffcc00;
  }
  #now-playing {
    margin-top:10px;
    font-size:1.1em;
    color:#ffcc00;
  }
  h2 {
    color:#ffcc00;
    margin:20px 0 10px;
    border-bottom:2px solid #ffcc00;
    padding-bottom:5px;
  }
  .section { margin-bottom:40px; }
  .channels-grid {
    display:grid;
    grid-template-columns:repeat(auto-fill, minmax(140px,1fr));
    gap:15px;
  }
  .channel-card {
    background-color:#1a1a1a;
    border:2px solid #333;
    border-radius:12px;
    padding:15px 10px;
    text-align:center;
    cursor:pointer;
    transition:transform 0.3s,border-color 0.3s,box-shadow 0.3s;
    display:flex;
    flex-direction:column;
    align-items:center;
    position:relative;
  }
  .channel-card span {
    font-size:2.5em;
    font-weight:bold;
    width:60px;
    height:60px;
    border-radius:50%;
    display:flex;
    align-items:center;
    justify-content:center;
    margin-bottom:10px;
    color:#fff;
  }
  .channel-card h3 { font-size:0.95em; color:#ffcc00; margin-bottom:5px; text-align:center; }
  .channel-card p { font-size:0.8em; color:#ccc; }
  .channel-card:hover { transform:scale(1.05); border-color:#ffcc00; box-shadow:0 0 15px #ffcc00; }
  .channel-card.active { border-color:#ffcc00; box-shadow:0 0 20px #ffcc00; }
  #no-channels { text-align:center; color:#ccc; margin-top:10px; font-size:1em; }
  @media(max-width:600px) { .channels-grid { grid-template-columns:repeat(auto-fill,minmax(120px,1fr)); } }
</style>
<script src="https://cdn.jsdelivr.net/npm/hls.js@latest"></script>
</head>
<body>

<h1>Mohamed Amin TV Pro</h1>

<div id="search-container">
  <input type="text" id="search-input" placeholder="Search channels...">
</div>

<div id="sections-nav"></div>

<div class="video-container">
  <video id="video" controls></video>
  <div id="now-playing">Select a channel to watch</div>
</div>

<div id="sections-container"></div>
<div id="no-channels" style="display:none;">No channels found</div>

<script>
const channels = [
  // Movies & Entertainment
  {name:"Aflam 3", link:"https://shls-live-enc.edgenextcdn.net/out/v1/46079e838e65490c8299f902a7731168/index.m3u8", section:"Movies & Entertainment"},
  {name:"Aflam 2", link:"https://shls-live-enc.edgenextcdn.net/out/v1/f6d718e841f8442f8374de47f18c93a7/index.m3u8", section:"Movies & Entertainment"},
  {name:"Aflam", link:"https://shls-live-enc.edgenextcdn.net/out/v1/0044dd4b001a466c941ad77b04a574a2/index.m3u8", section:"Movies & Entertainment"},
  // News
  {name:"Al Jazeera", link:"https://live-hls-apps-aja-fa.getaj.net/AJA/index.m3u8", section:"News"},
  {name:"Al Jazeera Mubashir", link:"https://live-hls-apps-ajm-fa.getaj.net/AJM/index.m3u8", section:"News"},
  {name:"Al Arabiya", link:"https://live.alarabiya.net/alarabiapublish/alarabiya.smil/playlist.m3u8", section:"News"},
  {name:"Chada TV", link:"https://chadatv.vedge.infomaniak.com/livecast/chadatv/playlist.m3u8", section:"News"},
  // Entertainment
  {name:"MBC", link:"https://shd-gcp-live.edgenextcdn.net/live/bitmovin-mbc-1-na/eec141533c90dd34722c503a296dd0d8/index.m3u8", section:"Entertainment"},
  {name:"Spacetoon", link:"https://shd-gcp-live.edgenextcdn.net/live/bitmovin-spacetoon/d8382fb9ab4b2307058f12c7ea90db54/index.m3u8", section:"Entertainment"},
  {name:"MBC Bollywood", link:"https://shd-gcp-live.edgenextcdn.net/live/bitmovin-mbc-bollywood/546eb40d7dcf9a209255dd2496903764/index.m3u8", section:"Entertainment"},
  {name:"MBC 4", link:"https://shd-gcp-live.edgenextcdn.net/live/bitmovin-mbc-4/24f134f1cd63db9346439e96b86ca6ed/index.m3u8", section:"Entertainment"},
  {name:"MBC 5", link:"https://shd-gcp-live.edgenextcdn.net/live/bitmovin-mbc-5/ee6b000cee0629411b666ab26cb13e9b/index.m3u8", section:"Entertainment"}
];

const sections = ["Movies & Entertainment","News","Sports","Entertainment"];
const container = document.getElementById('sections-container');
const video = document.getElementById('video');
const nowPlaying = document.getElementById('now-playing');
const searchInput = document.getElementById('search-input');
const sectionsNav = document.getElementById('sections-nav');
const noChannels = document.getElementById('no-channels');

let activeCard = null;

// Generate colors for letters consistently
function getColor(letter){
  const colors = ["#ff5733","#33ff57","#3357ff","#ff33a6","#33fff2","#f2ff33"];
  return colors[letter.charCodeAt(0)%colors.length];
}

// Create section buttons
sections.forEach(sec=>{
  const btn = document.createElement('button');
  btn.className='section-button';
  btn.innerText=sec;
  btn.onclick=()=>document.getElementById(sec).scrollIntoView({behavior:"smooth"});
  sectionsNav.appendChild(btn);
});

function renderSections(filter=""){
  container.innerHTML="";
  let anyVisible=false;
  sections.forEach(sectionName=>{
    const sectionDiv=document.createElement('div');
    sectionDiv.className='section';
    sectionDiv.id=sectionName;
    const title=document.createElement('h2');
    title.textContent=sectionName;
    sectionDiv.appendChild(title);
    const grid=document.createElement('div');
    grid.className='channels-grid';

    const filtered=channels.filter(c=>c.section===sectionName && c.name.toLowerCase().includes(filter.toLowerCase()));
    if(filtered.length>0) anyVisible=true;

    filtered.forEach(channel=>{
      const card=document.createElement('div');
      card.className='channel-card';
      const letter=channel.name.charAt(0).toUpperCase();
      const color=getColor(letter);
      card.innerHTML=`<span style="background:${color}">${letter}</span><h3>${channel.name}</h3><p>Click to watch</p>`;
      card.onclick=()=>playStream(channel.link,channel.name,card);
      grid.appendChild(card);
    });

    sectionDiv.appendChild(grid);
    container.appendChild(sectionDiv);
  });
  noChannels.style.display=anyVisible?"none":"block";
}

function playStream(link,name,card){
  if(activeCard) activeCard.classList.remove('active');
  card.classList.add('active');
  activeCard=card;
  nowPlaying.textContent=`Now Playing: ${name}`;
  if(Hls.isSupported()){
    const hls=new Hls();
    hls.loadSource(link);
    hls.attachMedia(video);
    hls.on(Hls.Events.MANIFEST_PARSED,()=>video.play());
  } else if(video.canPlayType('application/vnd.apple.mpegurl')){
    video.src=link;
    video.play();
  } else { alert('HLS not supported in this browser'); }
}

searchInput.addEventListener('input',(e)=>renderSections(e.target.value));
renderSections();
</script>

</body>
</html>
