<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Mohamed Amin TV</title>
<link href="https://fonts.googleapis.com/css2?family=Roboto:wght@400;700&display=swap" rel="stylesheet">
<style>
  * { box-sizing: border-box; margin: 0; padding: 0; }
  body {
    font-family: 'Roboto', sans-serif;
    background-color: #0d0d0d;
    color: #fff;
    padding: 20px;
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
  }

  #search-input {
    padding: 10px;
    width: 90%;
    max-width: 500px;
    border-radius: 8px;
    border: 2px solid #333;
    font-size: 1em;
  }

  h2 {
    color: #ffcc00;
    margin: 20px 0 10px;
    border-bottom: 2px solid #ffcc00;
    padding-bottom: 5px;
  }

  .section {
    margin-bottom: 40px;
  }

  .channels-grid {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(160px, 1fr));
    gap: 15px;
  }

  .channel-card {
    background-color: #1a1a1a;
    border: 2px solid #333;
    border-radius: 12px;
    padding: 10px;
    text-align: center;
    cursor: pointer;
    transition: transform 0.3s, border-color 0.3s, box-shadow 0.3s;
    display: flex;
    flex-direction: column;
    align-items: center;
  }

  .channel-card img {
    width: 100%;
    max-width: 120px;
    height: 80px;
    object-fit: contain;
    margin-bottom: 8px;
    border-radius: 8px;
    border: 1px solid #555;
  }

  .channel-card h3 {
    font-size: 0.95em;
    color: #ffcc00;
    margin-bottom: 5px;
    text-align: center;
  }

  .channel-card p {
    font-size: 0.8em;
    color: #ccc;
  }

  .channel-card:hover {
    transform: scale(1.05);
    border-color: #ffcc00;
    box-shadow: 0 0 15px #ffcc00;
  }

  .channel-card.active {
    border-color: #ffcc00;
    box-shadow: 0 0 20px #ffcc00;
  }

  .video-container {
    display: flex;
    flex-direction: column;
    align-items: center;
    margin-bottom: 40px;
  }

  video {
    width: 90%;
    max-width: 900px;
    border: 3px solid #ffcc00;
    border-radius: 15px;
    background-color: black;
    box-shadow: 0 0 20px #ffcc00;
  }

  #now-playing {
    margin-top: 10px;
    font-size: 1.1em;
    color: #ffcc00;
  }

  @media (max-width: 600px) {
    .channels-grid {
      grid-template-columns: repeat(auto-fill, minmax(140px, 1fr));
    }
  }
</style>
<script src="https://cdn.jsdelivr.net/npm/hls.js@latest"></script>
</head>
<body>

<h1>Mohamed Amin TV</h1>

<div id="search-container">
  <input type="text" id="search-input" placeholder="Search channels...">
</div>

<div class="video-container">
  <video id="video" controls></video>
  <div id="now-playing">Select a channel to watch</div>
</div>

<div id="sections-container"></div>

<script>
  const channels = [
    // Movies & Entertainment
    { name: "Aflam 3", link: "https://shls-live-enc.edgenextcdn.net/out/v1/46079e838e65490c8299f902a7731168/index.m3u8", section: "Movies & Entertainment", img: "https://via.placeholder.com/120x80?text=Aflam+3" },
    { name: "Aflam 2", link: "https://shls-live-enc.edgenextcdn.net/out/v1/f6d718e841f8442f8374de47f18c93a7/index.m3u8", section: "Movies & Entertainment", img: "https://via.placeholder.com/120x80?text=Aflam+2" },
    { name: "Aflam", link: "https://shls-live-enc.edgenextcdn.net/out/v1/0044dd4b001a466c941ad77b04a574a2/index.m3u8", section: "Movies & Entertainment", img: "https://via.placeholder.com/120x80?text=Aflam" },

    // News
    { name: "Al Jazeera", link: "https://live-hls-apps-aja-fa.getaj.net/AJA/index.m3u8", section: "News", img: "https://upload.wikimedia.org/wikipedia/commons/0/0f/Al_Jazeera.svg" },
    { name: "Al Jazeera Mubashir", link: "https://live-hls-apps-ajm-fa.getaj.net/AJM/index.m3u8", section: "News", img: "https://upload.wikimedia.org/wikipedia/commons/e/e7/Al_Jazeera_Mubasher.svg" },
    { name: "Al Arabiya", link: "https://live.alarabiya.net/alarabiapublish/alarabiya.smil/playlist.m3u8", section: "News", img: "https://upload.wikimedia.org/wikipedia/en/1/1b/Al_Arabiya_logo.png" },
    { name: "Chada TV", link: "https://chadatv.vedge.infomaniak.com/livecast/chadatv/playlist.m3u8", section: "News", img: "https://via.placeholder.com/120x80?text=Chada" },

    // Entertainment
    { name: "MBC", link: "https://shd-gcp-live.edgenextcdn.net/live/bitmovin-mbc-1-na/eec141533c90dd34722c503a296dd0d8/index.m3u8", section: "Entertainment", img: "https://upload.wikimedia.org/wikipedia/en/5/5b/MBC_logo.svg" },
    { name: "Spacetoon", link: "https://shd-gcp-live.edgenextcdn.net/live/bitmovin-spacetoon/d8382fb9ab4b2307058f12c7ea90db54/index.m3u8", section: "Entertainment", img: "https://upload.wikimedia.org/wikipedia/en/7/74/Spacetoon_logo.png" },
    { name: "MBC Bollywood", link: "https://shd-gcp-live.edgenextcdn.net/live/bitmovin-mbc-bollywood/546eb40d7dcf9a209255dd2496903764/index.m3u8", section: "Entertainment", img: "https://upload.wikimedia.org/wikipedia/en/d/d0/MBC_Bollywood_logo.png" },
    { name: "MBC 4", link: "https://shd-gcp-live.edgenextcdn.net/live/bitmovin-mbc-4/24f134f1cd63db9346439e96b86ca6ed/index.m3u8", section: "Entertainment", img: "https://upload.wikimedia.org/wikipedia/en/6/6e/MBC4_logo.png" },
    { name: "MBC 5", link: "https://shd-gcp-live.edgenextcdn.net/live/bitmovin-mbc-5/ee6b000cee0629411b666ab26cb13e9b/index.m3u8", section: "Entertainment", img: "https://upload.wikimedia.org/wikipedia/en/5/52/MBC5_logo.png" }
  ];

  const sections = ["Movies & Entertainment", "News", "Sports", "Entertainment"];
  const container = document.getElementById('sections-container');
  const video = document.getElementById('video');
  const nowPlaying = document.getElementById('now-playing');
  const searchInput = document.getElementById('search-input');

  let activeCard = null;

  function renderSections(filter="") {
    container.innerHTML = "";
    sections.forEach(sectionName => {
      const sectionDiv = document.createElement('div');
      sectionDiv.className = 'section';
      const title = document.createElement('h2');
      title.textContent = sectionName;
      sectionDiv.appendChild(title);

      const grid = document.createElement('div');
      grid.className = 'channels-grid';

      channels.filter(c => c.section === sectionName && c.name.toLowerCase().includes(filter.toLowerCase()))
              .forEach(channel => {
        const card = document.createElement('div');
        card.className = 'channel-card';
        card.innerHTML = `<img src="${channel.img}" alt="${channel.name}"><h3>${channel.name}</h3><p>Click to watch</p>`;
        card.onclick = () => playStream(channel.link, channel.name, card);
        grid.appendChild(card);
      });

      sectionDiv.appendChild(grid);
      container.appendChild(sectionDiv);
    });
  }

  function playStream(link, name, card) {
    if (activeCard) activeCard.classList.remove('active');
    card.classList.add('active');
    activeCard = card;

    nowPlaying.textContent = `Now Playing: ${name}`;

    if (Hls.isSupported()) {
      const hls = new Hls();
      hls.loadSource(link);
      hls.attachMedia(video);
      hls.on(Hls.Events.MANIFEST_PARSED, () => video.play());
    } else if (video.canPlayType('application/vnd.apple.mpegurl')) {
      video.src = link;
      video.play();
    } else {
      alert('HLS not supported in this browser');
    }
  }

  searchInput.addEventListener('input', (e) => {
    renderSections(e.target.value);
  });

  renderSections();
</script>

</body>
</html>
