<!doctype html>
<html lang="en">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>ZENO LAND STORE</title>
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600;800&display=swap" rel="stylesheet">
<style>
  :root{
    --bg:#08020a;
    --panel:rgba(255,255,255,0.04);
    --glass-border:rgba(255,255,255,0.06);
    --accent1:#9b5cff;
    --accent2:#4b2bd6;
    --muted:rgba(234,240,255,0.65);
    --glass-shadow:0 10px 50px rgba(2,3,10,0.7);
    --card-h:260px;
    font-family:Inter,system-ui,Segoe UI,Roboto,Arial;
  }
  *{box-sizing:border-box}
  html,body{height:100%;margin:0;background:var(--bg);color:#eaf0ff;overflow:hidden}
  a{color:inherit;text-decoration:none}
  button{font-weight:700;border-radius:8px;padding:8px 12px;border:none;cursor:pointer;background:linear-gradient(90deg,var(--accent1),var(--accent2));color:white}
  button.ghost{background:transparent;border:1px solid rgba(255,255,255,0.06);color:var(--muted)}
  /* layout */
  .stage{position:relative;width:100%;height:100vh;overflow:auto;z-index:2;padding-bottom:40px}
  .bg-effects{position:fixed;left:0;top:0;width:100%;height:100%;pointer-events:none;z-index:0}
  .glow{position:absolute;border-radius:50%;filter:blur(80px);opacity:0.28;animation:floaty 6s ease-in-out infinite alternate}
  .g1{width:520px;height:520px;left:5%;top:6%;background:radial-gradient(circle,#6f42ff,transparent)}
  .g2{width:420px;height:420px;right:5%;bottom:8%;background:radial-gradient(circle,#ff86e1,transparent)}
  .g3{width:640px;height:640px;left:40%;top:40%;background:radial-gradient(circle,#9b5cff,transparent);animation-delay:2s}
  @keyframes floaty{0%{transform:translateY(0)}50%{transform:translateY(-12px)}100%{transform:translateY(0)}}

  /* meteors canvas under content but above glows */
  #meteorsCanvas{position:fixed;left:0;top:0;width:100%;height:100%;pointer-events:none;z-index:1}

  /* main wrapper (content sits above canvas) */
  .wrap{max-width:1200px;margin:18px auto;padding:20px;position:relative;z-index:3}
  header{display:flex;align-items:center;gap:16px;margin-bottom:18px}
  .logo{display:flex;gap:12px;align-items:center}
  .badge{width:64px;height:64px;border-radius:12px;background:linear-gradient(135deg,var(--accent1),var(--accent2));display:flex;align-items:center;justify-content:center;font-weight:900;color:white;box-shadow:0 10px 40px rgba(75,43,214,0.25)}
  h1{font-size:20px;margin:0}
  .sub{color:var(--muted);font-size:13px}
  nav{margin-left:auto;display:flex;gap:8px;align-items:center}
  nav button{background:transparent;border-radius:10px;padding:8px 12px;border:1px solid transparent;color:var(--muted);font-weight:700}
  nav button.active{color:white;border:1px solid rgba(155,92,255,0.1);background:linear-gradient(90deg,rgba(155,92,255,0.04),transparent)}
  /* page container */
  .page{display:none}
  .page.active{display:block;animation:fadeIn .28s ease both}
  @keyframes fadeIn{from{opacity:0;transform:translateY(6px)}to{opacity:1;transform:none}}

  /* HOME layout */
  .hero{display:grid;grid-template-columns:1fr 360px;gap:18px;align-items:start;min-height:320px}
  .card{background:var(--panel);border-radius:14px;padding:18px;border:1px solid var(--glass-border);box-shadow:var(--glass-shadow)}
  .hero-main{min-height:var(--card-h);display:flex;flex-direction:column;gap:12px}
  .hero-visual{flex:1;border-radius:12px;background:linear-gradient(180deg,rgba(255,255,255,0.02),transparent);display:flex;align-items:center;justify-content:center;min-height:200px;position:relative;overflow:hidden}
  .hero-visual .fake-scene{width:100%;height:100%;display:grid;grid-template-columns:repeat(3,1fr);gap:8px;padding:12px}
  .fake-card{background:linear-gradient(180deg,rgba(255,255,255,0.02),transparent);border-radius:10px;padding:8px;display:flex;flex-direction:column;gap:6px;align-items:center;justify-content:center;min-height:80px}
  .news-grid{display:grid;grid-template-columns:repeat(auto-fit,minmax(200px,1fr));gap:12px;margin-top:12px}
  .news-item{padding:12px;border-radius:10px;background:linear-gradient(180deg,rgba(255,255,255,0.02),transparent);border:1px solid rgba(255,255,255,0.03)}
  .builds-grid{display:grid;grid-template-columns:repeat(auto-fit,minmax(200px,1fr));gap:12px;margin-top:12px}
  .build-card{min-height:120px;border-radius:10px;padding:10px;background:linear-gradient(180deg,rgba(255,255,255,0.01),transparent);border:1px solid rgba(255,255,255,0.03);display:flex;flex-direction:column;gap:8px}

  /* RANKS page */
  .ranks-grid{display:grid;grid-template-columns:repeat(auto-fit,minmax(220px,1fr));gap:14px;margin-top:12px}
  .rank-card{padding:14px;border-radius:12px;background:linear-gradient(180deg,rgba(255,255,255,0.02),transparent);display:flex;flex-direction:column;align-items:center;gap:8px;border:1px solid rgba(255,255,255,0.03)}
  .rank-icon{width:88px;height:88px;border-radius:14px;background-size:cover;background-position:center;box-shadow:0 8px 30px rgba(75,43,214,0.12)}

  /* CRATES page */
  .crates-grid{display:grid;grid-template-columns:repeat(auto-fit,minmax(140px,1fr));gap:14px;margin-top:12px}
  .crate-card{padding:12px;border-radius:12px;background:linear-gradient(180deg,rgba(255,255,255,0.01),transparent);display:flex;flex-direction:column;align-items:center;gap:8px;border:1px solid rgba(255,255,255,0.03)}
  .shulker-svg{width:88px;height:88px}

  /* modal (dark glass) */
  .modal-back{position:fixed;inset:0;background:rgba(2,3,10,0.6);display:flex;align-items:center;justify-content:center;z-index:40;backdrop-filter:blur(6px)}
  .modal{width:420px;max-width:92%;background:linear-gradient(180deg,rgba(255,255,255,0.02),rgba(255,255,255,0.01));border-radius:12px;padding:18px;border:1px solid rgba(255,255,255,0.06);box-shadow:var(--glass-shadow)}
  .modal h3{margin:0 0 8px 0}
  .field{display:flex;flex-direction:column;gap:6px;margin-top:8px}
  .field input{padding:10px;border-radius:8px;border:1px solid rgba(255,255,255,0.06);background:transparent;color:white;outline:none}
  .modal .row{display:flex;gap:8px;margin-top:12px}
  .small-muted{font-size:13px;color:var(--muted)}

  /* cart area on right of header in responsive */
  .top-strip{display:flex;gap:12px;align-items:center;margin-top:10px}
  .cart-bubble{background:rgba(255,255,255,0.03);padding:8px 10px;border-radius:999px;border:1px solid rgba(255,255,255,0.03)}
  .cart-list{margin-top:8px;font-size:14px;color:var(--muted)}

  /* footer */
  footer{margin-top:28px;text-align:center;color:var(--muted);font-size:13px;padding-bottom:28px}

  @media (max-width:980px){
    .hero{grid-template-columns:1fr;gap:12px}
    .wrap{padding:14px}
  }
</style>
</head>
<body>

  <!-- background glows -->
  <div class="bg-effects" aria-hidden="true">
    <div class="glow g1"></div>
    <div class="glow g2"></div>
    <div class="glow g3"></div>
  </div>

  <!-- meteors canvas (under UI) -->
  <canvas id="meteorsCanvas"></canvas>

  <!-- UI content (above meteors) -->
  <div class="stage" id="stage">
    <div class="wrap">
      <header>
        <div class="logo">
          <div class="badge">Z</div>
          <div>
            <h1>ZENO LAND STORE</h1>
            <div class="sub">Tough. Amethyst. Shards.</div>
          </div>
        </div>

        <nav id="nav">
          <button data-page="home" class="active">Home</button>
          <button data-page="ranks">Ranks</button>
          <button data-page="crates">Crates</button>
        </nav>
      </header>

      <!-- Top strip: cart quick -->
      <div class="top-strip">
        <div class="cart-bubble" id="cartBubble">Cart: <strong id="cartCount">0</strong></div>
        <div style="margin-left:auto" class="small-muted">Server IP: <strong>zeno.mc.land</strong></div>
      </div>

      <!-- PAGES -->
      <!-- HOME -->
      <section id="home" class="page active">
        <div class="hero">
          <div class="hero-main card">
            <div style="display:flex;justify-content:space-between;align-items:center">
              <div>
                <div style="font-weight:800;font-size:20px">Welcome to Zeno Land Store</div>
                <div class="small-muted" style="margin-top:6px">PvP nights, giveaways, creative builds — everything in a tough, clean package.</div>
              </div>
              <div style="text-align:right">
                <div style="font-weight:800">Join the event</div>
                <div class="small-muted">This Friday — 9 PM server time</div>
                <div style="margin-top:8px"><button onclick="alert('Bookmark the event!')">Add reminder</button></div>
              </div>
            </div>

            <div class="hero-visual" aria-hidden="true">
              <!-- stylized concept blocks (no external images) -->
              <div class="fake-scene" role="presentation">
                <div class="fake-card">
                  <div style="font-weight:800">PvP Duel</div>
                  <div class="small-muted">2 players fighting — arena mode</div>
                </div>
                <div class="fake-card">
                  <div style="font-weight:800">Team Base</div>
                  <div class="small-muted">Collaborative building</div>
                </div>
                <div class="fake-card">
                  <div style="font-weight:800">Mining Trip</div>
                  <div class="small-muted">Find the rare ores</div>
                </div>
              </div>
            </div>

            <div style="display:flex;gap:12px;margin-top:12px">
              <div style="flex:1" class="card">
                <div style="font-weight:800">Latest News</div>
                <div class="news-grid" id="newsGrid">
                  <!-- dynamic news -->
                </div>
              </div>

              <div style="width:360px" class="card">
                <div style="font-weight:800">Player Builds</div>
                <div class="builds-grid" id="buildsGrid">
                  <!-- dynamic builds -->
                </div>
              </div>
            </div>
          </div>

          <aside class="card" style="height:100%;">
            <div style="font-weight:800">Quick Actions</div>
            <div style="margin-top:12px" class="small-muted">
              View ranks, open crates, or make a purchase — all on this site.
            </div>

            <div style="margin-top:14px">
              <button onclick="navigateTo('ranks')">View Ranks</button>
            </div>

            <div style="margin-top:12px">
              <button class="ghost" onclick="navigateTo('crates')">See Crates</button>
            </div>

            <div style="margin-top:16px" class="card" aria-hidden="true">
              <div style="font-weight:800">Upcoming</div>
              <div class="small-muted" style="margin-top:6px">Giveaway drops — Sundays</div>
              <div class="small-muted">Map reset — next month</div>
            </div>
          </aside>
        </div>
      </section>

      <!-- RANKS -->
      <section id="ranks" class="page">
        <div class="card">
          <div style="display:flex;justify-content:space-between;align-items:center">
            <div style="font-weight:900;font-size:18px">Ranks</div>
            <div class="small-muted">Choose your power</div>
          </div>

          <div class="ranks-grid" id="ranksGrid">
            <!-- ranks injected by JS -->
          </div>
        </div>
      </section>

      <!-- CRATES -->
      <section id="crates" class="page">
        <div class="card">
          <div style="display:flex;justify-content:space-between;align-items:center">
            <div style="font-weight:900;font-size:18px">Crates</div>
            <div class="small-muted">Open to win</div>
          </div>

          <div class="crates-grid" id="cratesGrid">
            <!-- crates injected by JS -->
          </div>
        </div>
      </section>

      <footer>© ZENO LAND STORE — Built tough</footer>
    </div>
  </div>

  <!-- shared buy modal (dark-glass) -->
  <div id="modalBack" class="modal-back" style="display:none">
    <div class="modal" role="dialog" aria-modal="true" aria-labelledby="modalTitle">
      <h3 id="modalTitle">Buy — <span id="modalItem"></span></h3>
      <div class="small-muted" id="modalPrice">Price</div>

      <div class="field">
        <label class="small-muted">Your Minecraft name</label>
        <input id="mcName" placeholder="e.g. qhxstzz" autocomplete="off" />
      </div>

      <div class="row" style="justify-content:flex-end">
        <button onclick="confirmBuy()">Confirm</button>
        <button class="ghost" onclick="closeModal()">Cancel</button>
      </div>

      <div id="modalResult" style="margin-top:10px;display:none" class="small-muted"></div>
    </div>
  </div>

<script>
/* ---------- SPA Navigation ---------- */
const navButtons = document.querySelectorAll('nav button');
const pages = document.querySelectorAll('.page');
function showPage(id){
  pages.forEach(p => p.id === id ? p.classList.add('active') : p.classList.remove('active'));
  navButtons.forEach(b => b.classList.toggle('active', b.dataset.page === id));
  // scroll top of stage to top for nicer feel
  document.getElementById('stage').scrollTo({top:0, behavior:'smooth'});
}
function navigateTo(id){
  showPage(id);
}
navButtons.forEach(b => b.addEventListener('click', () => navigateTo(b.dataset.page)));


// default page
showPage('home');

/* ---------- DYNAMIC DATA (news + builds + ranks + crates) ---------- */

// sample news items
const newsList = [
  {title: "⚔️ PvP Event — Friday", text: "Join the PvP duels this Friday at 21:00 server time. Rewards for top 3!"},
  {title: "🎁 Giveaway Drop", text: "Weekly crate giveaway — drop every Sunday. Stay active to win!"},
  {title: "🛠️ Map Update", text: "New community plots added for team builds."}
];
const buildsList = [
  {title: "Sky Fortress", author: "BuilderOne"},
  {title: "Underground City", author: "MinesRUs"},
  {title: "Crystal Tower", author: "AmethystLord"},
  {title: "Starter Village", author: "TeamAlpha"}
];

const ranksList = [
  {id:'amethyst', name:'Amethyst', price:19.99, desc:'Highest rank — VIP perks'},
  {id:'vortex', name:'Vortex', price:12.99, desc:'Priority queue & extras'},
  {id:'obsidian', name:'Obsidian', price:9.99, desc:'Mid-tier perks'},
  {id:'shard', name:'Shard', price:4.99, desc:'Starter rank'}
];

const cratesList = [
  {id:'common', name:'Common', color:'#49b764', price:2.99},
  {id:'rare', name:'Rare', color:'#3fa6ff', price:4.99},
  {id:'legendary', name:'Legendary', color:'#ff9b2f', price:8.99},
  {id:'elite', name:'Elite', color:'#b34cff', price:14.99},
  {id:'exclusive', name:'Exclusive', color:'#ff4c88', price:24.99}
];

/* populate news */
const newsGrid = document.getElementById('newsGrid');
newsList.forEach(n => {
  const d = document.createElement('div');
  d.className = 'news-item';
  d.innerHTML = `<div style="font-weight:800">${n.title}</div><div class="small-muted" style="margin-top:6px">${n.text}</div>`;
  newsGrid.appendChild(d);
});

/* populate builds */
const buildsGrid = document.getElementById('buildsGrid');
buildsList.forEach(b => {
  const c = document.createElement('div');
  c.className = 'build-card';
  c.innerHTML = `<div style="font-weight:800">${b.title}</div><div class="small-muted">by ${b.author}</div><div style="flex:1;display:flex;align-items:center;justify-content:center;color:var(--muted)">[Preview]</div>`;
  buildsGrid.appendChild(c);
});

/* populate ranks */
const ranksGrid = document.getElementById('ranksGrid');
ranksList.forEach(r => {
  // simple SVG gem/icon as data URL
  const svg = encodeURIComponent(`<svg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 64 64'><polygon points='32,6 46,18 40,46 24,46 18,18' fill='${r.id === 'amethyst' ? '#8b5cff' : r.id === 'vortex' ? '#6b2be0' : r.id === 'obsidian' ? '#111' : '#b36cff'}'/></svg>`);
  const node = document.createElement('div');
  node.className = 'rank-card';
  node.innerHTML = `
    <div class="rank-icon" style="background-image:url('data:image/svg+xml;utf8,${svg}');background-color:rgba(255,255,255,0.02)"></div>
    <div style="font-weight:800">${r.name}</div>
    <div class="small-muted">${r.desc}</div>
    <div style="margin-top:8px;width:100%"><button style="width:100%" onclick="openBuyModal('${r.name}', ${r.price}, 'rank')">Buy • $${r.price.toFixed(2)}</button></div>
  `;
  ranksGrid.appendChild(node);
});

/* populate crates */
const cratesGrid = document.getElementById('cratesGrid');
cratesList.forEach(c => {
  // create small shulker SVG with chosen color
  const svg = encodeURIComponent(`<svg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 120 120'><rect x='10' y='36' width='100' height='60' rx='10' fill='${c.color}' stroke='#000' stroke-opacity='0.12'/><rect x='18' y='26' width='84' height='20' rx='6' fill='${c.color}' stroke='#000' stroke-opacity='0.09'/></svg>`);
  const node = document.createElement('div');
  node.className = 'crate-card';
  node.innerHTML = `
    <div class="shulker-svg"><img src="data:image/svg+xml;utf8,${svg}" alt="${c.name}" style="width:88px;height:88px"></div>
    <div style="font-weight:800">${c.name}</div>
    <div class="small-muted">$${c.price.toFixed(2)}</div>
    <div style="width:100%;margin-top:8px"><button style="width:100%" onclick="openBuyModal('${c.name} Crate', ${c.price}, 'crate')">Buy</button></div>
  `;
  cratesGrid.appendChild(node);
});

/* ---------- BUY modal & cart ---------- */
let currentBuy = null;
const modalBack = document.getElementById('modalBack');
const modalItem = document.getElementById('modalItem');
const modalPrice = document.getElementById('modalPrice');
const mcNameInput = document.getElementById('mcName');
const modalResult = document.getElementById('modalResult');
const cart = []; // simple in-memory cart
const cartCount = document.getElementById('cartCount');
const cartBubble = document.getElementById('cartBubble');

function openBuyModal(itemName, price, type){
  currentBuy = {itemName, price, type};
  modalItem.textContent = itemName;
  modalPrice.textContent = `$${price.toFixed(2)}`;
  mcNameInput.value = '';
  modalResult.style.display = 'none';
  modalBack.style.display = 'flex';
  setTimeout(()=>mcNameInput.focus(), 200);
}

function closeModal(){
  modalBack.style.display = 'none';
}

function confirmBuy(){
  const name = mcNameInput.value.trim();
  if(!name || name.length < 2){
    modalResult.style.display = 'block';
    modalResult.style.color = '#ffb3b3';
    modalResult.textContent = 'Please enter a valid Minecraft name.';
    return;
  }
  // add to cart and show confirmation
  cart.push({item: currentBuy.itemName, price: currentBuy.price, player: name, type: currentBuy.type});
  cartCount.textContent = cart.length;
  modalResult.style.display = 'block';
  modalResult.style.color = '#aef7c0';
  modalResult.textContent = `Thanks ${name}! ${currentBuy.itemName} added to your cart. We will contact you.`;
  // show simple entry in cart bubble details (small)
  const entry = document.createElement('div');
  entry.className = 'cart-list';
  entry.textContent = `${currentBuy.itemName} — ${name} — $${currentBuy.price.toFixed(2)}`;
  cartBubble.parentElement.appendChild(entry);
  // auto-close after a short delay
  setTimeout(() => {
    modalBack.style.display = 'none';
  }, 1200);
}

/* close modal by background click or ESC */
modalBack.addEventListener('click', (e) => { if(e.target === modalBack) closeModal(); });
document.addEventListener('keydown', e => { if(e.key === 'Escape') closeModal(); });

/* ---------- METEORS animation (1-3 every 6-7s, random positions, under content) ---------- */
const canvas = document.getElementById('meteorsCanvas');
const ctx = canvas.getContext('2d');
let meteors = [];
function resizeCanvas(){ canvas.width = window.innerWidth; canvas.height = window.innerHeight; }
window.addEventListener('resize', resizeCanvas);
resizeCanvas();

function spawnBurst(){
  // spawn 1-3
  const count = Math.floor(Math.random()*3) + 1;
  const headerRect = document.querySelector('header').getBoundingClientRect();
  const topLimit = Math.max(headerRect.bottom + 60, 140); // avoid header and top cards
  const bottomLimit = Math.min(window.innerHeight * 0.8, window.innerHeight - 120);
  for(let i=0;i<count;i++){
    // random x across width, but not too close to center buttons cluster - jitter more
    const x = Math.random() * (canvas.width - 80) + 40;
    const y = topLimit + Math.random() * Math.max(0, bottomLimit - topLimit);
    const size = 2 + Math.random() * 5;
    const angle = (Math.random()*0.6 - 0.3) - 0.6; // move down-right mostly
    const speed = 1 + Math.random()*2;
    meteors.push({
      x, y, vx: Math.cos(angle) * speed, vy: Math.sin(angle) * speed + 1.2, r: size,
      life: 0, maxLife: 300 + Math.random()*300
    });
  }
  // schedule next burst in 6000-8000 ms
  setTimeout(spawnBurst, 6000 + Math.random()*2000);
}
// initial spawn delay
setTimeout(spawnBurst, 1200);

function drawMeteor(m){
  // draw a soft glow head + elongated tail
  const trail = Math.max(6, m.r * 6);
  const tx = m.x - m.vx * trail * 2;
  const ty = m.y - m.vy * trail * 2;

  // gradient tail
  const g = ctx.createLinearGradient(tx, ty, m.x, m.y);
  g.addColorStop(0, 'rgba(255,255,255,0)');
  g.addColorStop(0.6, 'rgba(255,255,255,0.18)');
  g.addColorStop(1, 'rgba(255,255,255,0.95)');

  ctx.beginPath();
  ctx.moveTo(tx, ty);
  ctx.lineTo(m.x, m.y);
  ctx.lineWidth = m.r * 2;
  ctx.strokeStyle = g;
  ctx.lineCap = 'round';
  ctx.stroke();

  // bright head
  const grad = ctx.createRadialGradient(m.x, m.y, 0, m.x, m.y, m.r*4);
  grad.addColorStop(0, 'rgba(255,255,255,0.95)');
  grad.addColorStop(0.6, 'rgba(255,255,255,0.35)');
  grad.addColorStop(1, 'rgba(255,255,255,0)');
  ctx.beginPath();
  ctx.fillStyle = grad;
  ctx.arc(m.x, m.y, m.r*3, 0, Math.PI*2);
  ctx.fill();
}

function animate(){
  ctx.clearRect(0,0,canvas.width,canvas.height);
  for(let i=0;i<meteors.length;i++){
    const m = meteors[i];
    drawMeteor(m);
    m.x += m.vx * 2;
    m.y += m.vy * 2;
    m.vy += 0.02; // subtle gravity
    m.life++;
    // remove if off screen or too old
    if(m.y > canvas.height + 80 || m.x < -80 || m.x > canvas.width + 80 || m.life > m.maxLife){
      meteors.splice(i,1); i--;
    }
  }
  requestAnimationFrame(animate);
}
animate();

/* ---------- small UX helpers ---------- */
// clicking logo goes home
document.querySelector('.logo').addEventListener('click', () => navigateTo('home'));

// make header nav clickable via keyboard
navButtons.forEach(b => { b.tabIndex = 0; b.addEventListener('keydown', e => { if(e.key === 'Enter') b.click(); }) });

</script>
</body>
</html>
