# JalRakshakAI
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>JalRakshak AI — Smart Water Advisor</title>
<style>
  :root{
    --deep:#065A82; --teal:#1C7293; --midnight:#21295C; --ice:#EAF4F8;
    --accent:#4FC0D0; --text:#1A2B33; --white:#ffffff;
  }
  *{box-sizing:border-box;}
  body{
    margin:0; font-family:-apple-system,Segoe UI,Calibri,Arial,sans-serif;
    background:linear-gradient(160deg,var(--midnight) 0%, var(--deep) 45%, var(--teal) 100%);
    min-height:100vh; color:var(--text); padding:24px;
  }
  .app{
    max-width:980px; margin:0 auto; background:var(--ice); border-radius:18px;
    overflow:hidden; box-shadow:0 20px 60px rgba(0,0,0,0.35);
  }
  header{
    background:var(--midnight); color:var(--white); padding:22px 28px;
    display:flex; align-items:center; gap:14px;
  }
  header .drop{
    width:40px; height:40px; border-radius:50%; background:var(--accent);
    display:flex; align-items:center; justify-content:center; font-size:20px;
  }
  header h1{margin:0; font-size:20px;}
  header p{margin:2px 0 0; font-size:12.5px; color:#cfe9ef; opacity:0.85;}
  .layout{display:grid; grid-template-columns:1.4fr 1fr; gap:0;}
  @media (max-width:760px){ .layout{grid-template-columns:1fr;} }

  .chat-panel{padding:20px; display:flex; flex-direction:column; background:var(--white);}
  .messages{
    flex:1; min-height:340px; max-height:430px; overflow-y:auto; padding:8px 4px;
    display:flex; flex-direction:column; gap:10px;
  }
  .msg{max-width:85%; padding:10px 14px; border-radius:14px; font-size:13.5px; line-height:1.45;}
  .msg.bot{background:var(--ice); align-self:flex-start; border:1px solid #d8eaf0;}
  .msg.user{background:var(--deep); color:#fff; align-self:flex-end;}
  .msg b{color:var(--midnight);}
  .msg.bot b{color:var(--deep);}
  .tip-list{margin:6px 0 0; padding-left:18px;}
  .tip-list li{margin-bottom:4px;}
  .input-row{display:flex; gap:8px; margin-top:12px;}
  .input-row input{
    flex:1; padding:11px 14px; border-radius:10px; border:1.5px solid #cfe2e8; font-size:13.5px;
    outline:none;
  }
  .input-row input:focus{border-color:var(--teal);}
  .input-row button{
    background:var(--teal); color:#fff; border:none; padding:0 18px; border-radius:10px;
    font-weight:600; cursor:pointer; font-size:13.5px;
  }
  .input-row button:hover{background:var(--deep);}
  .chips{display:flex; gap:6px; flex-wrap:wrap; margin-top:10px;}
  .chip{
    background:var(--ice); border:1px solid #cfe2e8; border-radius:20px; padding:5px 12px;
    font-size:11.5px; cursor:pointer; color:var(--deep);
  }
  .chip:hover{background:#dcedf2;}

  .side-panel{background:var(--ice); padding:20px; border-left:1px solid #d8eaf0;}
  .card{
    background:#fff; border-radius:12px; padding:16px; margin-bottom:14px;
    box-shadow:0 4px 14px rgba(0,0,0,0.06);
  }
  .card h3{margin:0 0 10px; font-size:13.5px; color:var(--midnight); display:flex; align-items:center; gap:6px;}
  .score-ring{
    width:108px; height:108px; border-radius:50%; margin:6px auto 10px;
    display:flex; align-items:center; justify-content:center; position:relative;
  }
  .score-ring span{font-size:24px; font-weight:700; color:var(--midnight);}
  .score-label{text-align:center; font-size:11.5px; color:var(--teal); font-weight:600;}
  .stat-row{display:flex; justify-content:space-between; font-size:12.5px; padding:5px 0; border-bottom:1px dashed #e3eef2;}
  .stat-row:last-child{border-bottom:none;}
  .stat-row b{color:var(--deep);}
  .log-item{font-size:11.5px; padding:6px 0; border-bottom:1px solid #eef5f8; color:#3a4a52;}
  .log-item:last-child{border-bottom:none;}
  .empty{font-size:11.5px; color:#8aa0a8; font-style:italic;}
  .reset-btn{
    width:100%; background:none; border:1px solid #cfe2e8; color:var(--teal); padding:8px;
    border-radius:8px; font-size:12px; cursor:pointer; margin-top:4px;
  }
  .reset-btn:hover{background:#dcedf2;}
  footer{
    text-align:center; font-size:11px; color:#cfe9ef; padding:10px; background:var(--midnight);
  }
</style>
</head>
<body>
<div class="app">
  <header>
    <div class="drop">💧</div>
    <div>
      <h1>JalRakshak AI</h1>
      <p>Smart Water Usage & Conservation Advisor — SDG 6 Prototype</p>
    </div>
  </header>

  <div class="layout">
    <div class="chat-panel">
      <div class="messages" id="messages"></div>
      <div class="chips" id="chips"></div>
      <div class="input-row">
        <input id="userInput" type="text" placeholder="Describe your daily water usage... e.g. '20 min shower and laundry daily'" />
        <button onclick="handleSend()">Send</button>
      </div>
    </div>

    <div class="side-panel">
      <div class="card">
        <h3>🌿 Sustainability Score</h3>
        <div class="score-ring" id="scoreRing">
          <span id="scoreVal">--</span>
        </div>
        <div class="score-label" id="scoreLabel">Log an entry to begin</div>
      </div>

      <div class="card">
        <h3>📊 Today's Estimate</h3>
        <div class="stat-row"><span>Estimated usage</span><b id="estUsage">— L/day</b></div>
        <div class="stat-row"><span>City benchmark</span><b>135 L/day</b></div>
        <div class="stat-row"><span>Potential savings</span><b id="estSavings">— L/day</b></div>
      </div>

      <div class="card">
        <h3>🗒️ Usage Log</h3>
        <div id="logList"><div class="empty">No entries yet.</div></div>
        <button class="reset-btn" onclick="resetAll()">Reset session</button>
      </div>
    </div>
  </div>
  <footer>Prototype by Vandita Gupta · Kashi Institute of Technology · 1M1B AI for Sustainability Internship</footer>
</div>

<script>
// ---------- Simulated "RAG" knowledge base (local conservation guidelines) ----------
const KB = {
  shower: { litersPerMin: 9, tip: "Switch to bucket bathing or limit showers to 5 minutes — can save up to 40L/day." },
  laundry: { litersPerLoad: 60, tip: "Run laundry only with full loads, 2–3 times a week instead of daily — saves ~120L/week." },
  utensils: { litersPerWash: 20, tip: "Use a tub to rinse utensils instead of running tap water — saves ~15L/day." },
  car: { litersPerWash: 100, tip: "Use a bucket and sponge instead of a hose for washing vehicles — saves ~80L per wash." },
  garden: { litersPerSession: 50, tip: "Water plants early morning/evening and reuse RO-reject water — saves ~20L/day." },
  leak: { litersPerDay: 25, tip: "A small dripping tap can waste 25L/day — get leaks fixed immediately." },
  brushing: { litersPerSession: 5, tip: "Turn off the tap while brushing — saves up to 8L per session." },
  default: { litersPerDay: 20, tip: "Track this activity for a week to see its real impact on your total usage." }
};

const BENCHMARK = 135; // L/day per person, typical urban benchmark

let session = { entries: [], totalEstimated: 0 };

function addMessage(text, sender){
  const box = document.getElementById('messages');
  const div = document.createElement('div');
  div.className = 'msg ' + sender;
  div.innerHTML = text;
  box.appendChild(div);
  box.scrollTop = box.scrollHeight;
}

// ---------- Simple "entity extraction" ----------
function extractEntities(input){
  const text = input.toLowerCase();
  const found = [];

  const numMatch = (re) => {
    const m = text.match(re);
    return m ? parseInt(m[1]) : null;
  };

  if(text.includes('shower') || text.includes('bath')){
    const mins = numMatch(/(\d+)\s*(?:min|minute)/) || 10;
    found.push({type:'shower', qty: mins, unit:'minutes', liters: mins * KB.shower.litersPerMin});
  }
  if(text.includes('laundry') || text.includes('washing machine') || text.includes('clothes')){
    const loads = numMatch(/(\d+)\s*load/) || 1;
    found.push({type:'laundry', qty: loads, unit:'loads', liters: loads * KB.laundry.litersPerLoad});
  }
  if(text.includes('utensil') || text.includes('dish') || text.includes('vessel')){
    found.push({type:'utensils', qty:1, unit:'session', liters: KB.utensils.litersPerWash});
  }
  if(text.includes('car') || text.includes('bike') || text.includes('vehicle')){
    found.push({type:'car', qty:1, unit:'wash', liters: KB.car.litersPerWash});
  }
  if(text.includes('garden') || text.includes('plant') || text.includes('water the')){
    found.push({type:'garden', qty:1, unit:'session', liters: KB.garden.litersPerSession});
  }
  if(text.includes('leak') || text.includes('drip') || text.includes('tap running')){
    found.push({type:'leak', qty:1, unit:'leak', liters: KB.leak.litersPerDay});
  }
  if(text.includes('brush')){
    found.push({type:'brushing', qty:1, unit:'session', liters: KB.brushing.litersPerSession});
  }
  if(found.length === 0){
    found.push({type:'default', qty:1, unit:'activity', liters: KB.default.litersPerDay});
  }
  return found;
}

function computeScore(totalLiters){
  // lower usage relative to benchmark = higher score
  const ratio = totalLiters / BENCHMARK;
  let score = Math.round(100 - (ratio - 1) * 60);
  score = Math.max(10, Math.min(98, score));
  return score;
}

function scoreColor(score){
  if(score >= 75) return '#1C7293';
  if(score >= 50) return '#E2A93A';
  return '#C0392B';
}

function updateSidePanel(){
  const total = session.totalEstimated;
  document.getElementById('estUsage').textContent = total + ' L/day';
  const savingsPossible = Math.max(0, Math.round(total * 0.18));
  document.getElementById('estSavings').textContent = savingsPossible + ' L/day';

  const score = total > 0 ? computeScore(total) : null;
  const ring = document.getElementById('scoreRing');
  const val = document.getElementById('scoreVal');
  const label = document.getElementById('scoreLabel');

  if(score !== null){
    const c = scoreColor(score);
    ring.style.background = `conic-gradient(${c} ${score*3.6}deg, #e6eef0 0deg)`;
    val.textContent = score;
    val.style.color = c;
    label.textContent = score >= 75 ? 'Great — water-wise habits!' : score >= 50 ? 'Moderate — room to improve' : 'High usage — act on tips below';
  } else {
    ring.style.background = '#e6eef0';
    val.textContent = '--';
    label.textContent = 'Log an entry to begin';
  }

  const logList = document.getElementById('logList');
  if(session.entries.length === 0){
    logList.innerHTML = '<div class="empty">No entries yet.</div>';
  } else {
    logList.innerHTML = session.entries.slice().reverse().map(e =>
      `<div class="log-item"><b>${e.label}</b> — ${e.liters}L estimated</div>`
    ).join('');
  }
}

function handleSend(){
  const input = document.getElementById('userInput');
  const text = input.value.trim();
  if(!text) return;
  addMessage(text, 'user');
  input.value = '';
  setTimeout(() => respondTo(text), 350);
}

function respondTo(text){
  const entities = extractEntities(text);
  let totalLiters = 0;
  let tipsHtml = '<ul class="tip-list">';
  let labelParts = [];

  entities.forEach(e => {
    totalLiters += e.liters;
    const kbItem = KB[e.type] || KB.default;
    tipsHtml += `<li><b>${capitalize(e.type)}</b> (~${e.liters}L): ${kbItem.tip}</li>`;
    labelParts.push(capitalize(e.type));
  });
  tipsHtml += '</ul>';

  session.totalEstimated += totalLiters;
  session.entries.push({ label: labelParts.join(' + '), liters: totalLiters });

  const comparison = session.totalEstimated > BENCHMARK
    ? `That's <b>${session.totalEstimated - BENCHMARK}L above</b> the typical urban benchmark of ${BENCHMARK}L/day.`
    : `That's within (or below) the typical urban benchmark of ${BENCHMARK}L/day — nice work!`;

  const reply = `Estimated usage from this entry: <b>~${totalLiters}L</b>.<br>${comparison}${tipsHtml}`;
  addMessage(reply, 'bot');
  updateSidePanel();
}

function capitalize(s){ return s.charAt(0).toUpperCase() + s.slice(1); }

function resetAll(){
  session = { entries: [], totalEstimated: 0 };
  document.getElementById('messages').innerHTML = '';
  updateSidePanel();
  greet();
}

function greet(){
  addMessage("Namaste! I'm <b>JalRakshak AI</b> 💧 Tell me about your daily water-using activities (shower, laundry, dishes, garden, leaks...) and I'll estimate your usage and suggest ways to save water.", 'bot');
}

// quick-reply chips
const sampleChips = [
  "20 min shower daily",
  "2 laundry loads today",
  "Washed the car",
  "Tap is leaking",
  "Watered the garden"
];
const chipsBox = document.getElementById('chips');
sampleChips.forEach(c => {
  const el = document.createElement('div');
  el.className = 'chip';
  el.textContent = c;
  el.onclick = () => { document.getElementById('userInput').value = c; handleSend(); };
  chipsBox.appendChild(el);
});

document.getElementById('userInput').addEventListener('keydown', (e) => {
  if(e.key === 'Enter') handleSend();
});

greet();
updateSidePanel();
</script>
</body>
</html>
