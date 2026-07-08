<!DOCTYPE html>
<html lang="id">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1, user-scalable=no">
<title>Kalibrasi Gacoan Singosari</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Poppins:wght@600;700;800;900&family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
<style>
  :root{
    --pink: #EC1A6B;
    --pink-deep: #C4105A;
    --pink-tint: #FFF0F7;
    --blue: #4FC3E8;
    --blue-tint: #EAFAFF;
    --ink: #1A1A2E;
    --ink-soft: #5B5B6B;
    --white: #FFFFFF;
    --line: #F0E4EC;
    --good: #1FAF6E;
    --shadow: 0 8px 24px rgba(236,26,107,0.08);
  }
  *{box-sizing:border-box; -webkit-tap-highlight-color: transparent;}
  html,body{margin:0;padding:0;}
  body{
    font-family:'Inter', sans-serif;
    background: linear-gradient(180deg, var(--blue-tint) 0%, var(--white) 18%, var(--white) 100%);
    color: var(--ink);
    min-height:100vh;
    padding-bottom: 40px;
  }
  .app{max-width:520px; margin:0 auto; padding:0 18px;}

  /* HEADER */
  header.top{
    padding: 22px 18px 18px;
    max-width:520px;
    margin:0 auto;
  }
  .brand{
    display:flex; align-items:center; gap:10px;
  }
  .brand-mark{
    width:40px;height:40px;border-radius:12px;
    background: linear-gradient(135deg, var(--pink), var(--blue));
    display:flex;align-items:center;justify-content:center;
    font-family:'Poppins',sans-serif; font-weight:900; color:white; font-size:18px;
    flex-shrink:0;
    box-shadow: 0 6px 14px rgba(236,26,107,0.25);
  }
  .brand-text h1{
    font-family:'Poppins',sans-serif; font-weight:900; font-size:19px; margin:0; letter-spacing:-0.02em; line-height:1.1;
  }
  .brand-text p{margin:2px 0 0; font-size:12.5px; color:var(--ink-soft); font-weight:500;}

  .datetime-row{
    display:flex; gap:8px; margin-top:16px;
  }
  .dt-pill{
    flex:1; background:var(--white); border:1.5px solid var(--line); border-radius:14px;
    padding:10px 12px; font-size:13px; font-weight:600; color:var(--ink-soft);
    display:flex; align-items:center; justify-content:center; gap:6px;
  }
  .dt-pill b{color:var(--ink); font-weight:700;}

  /* CARD */
  .card{
    background: var(--white);
    border-radius: 22px;
    padding: 20px;
    margin-top: 16px;
    box-shadow: var(--shadow);
    border: 1.5px solid var(--line);
  }
  .card h2{
    font-family:'Poppins',sans-serif; font-weight:800; font-size:15px; margin:0 0 14px;
    display:flex; align-items:center; gap:8px; letter-spacing:-0.01em;
  }
  .card h2 .dot{width:8px;height:8px;border-radius:50%; background:var(--pink); flex-shrink:0;}

  label.field-label{
    display:block; font-size:12px; font-weight:700; color:var(--ink-soft);
    text-transform:uppercase; letter-spacing:0.04em; margin-bottom:7px;
  }

  input[type="text"], select{
    width:100%; padding:13px 14px; border-radius:14px; border:1.5px solid var(--line);
    background: var(--white); font-family:'Inter',sans-serif; font-size:15px; font-weight:600;
    color:var(--ink); outline:none; transition: border-color .15s ease, box-shadow .15s ease;
    appearance:none; -webkit-appearance:none;
  }
  input[type="text"]:focus, select:focus{
    border-color: var(--pink); box-shadow: 0 0 0 4px rgba(236,26,107,0.08);
  }
  select{
    background-image: url("data:image/svg+xml;utf8,<svg xmlns='http://www.w3.org/2000/svg' width='14' height='9' viewBox='0 0 14 9'><path d='M1 1L7 7L13 1' stroke='%235B5B6B' stroke-width='2' fill='none' stroke-linecap='round'/></svg>");
    background-repeat:no-repeat; background-position: right 14px center; padding-right: 38px;
  }

  .grid2{display:grid; grid-template-columns:1fr 1fr; gap:10px;}

  /* item chips */
  .item-chips{display:flex; flex-wrap:wrap; gap:8px; margin-top:10px;}
  .chip{
    padding:8px 14px; border-radius:100px; border:1.5px solid var(--line);
    font-size:13px; font-weight:700; color:var(--ink-soft); background:var(--white);
    cursor:pointer; transition: all .15s ease; user-select:none;
  }
  .chip.active{
    background: var(--pink); border-color:var(--pink); color:white;
    box-shadow: 0 4px 10px rgba(236,26,107,0.25);
  }
  .chip.custom-active{
    background: var(--blue); border-color:var(--blue); color:white;
  }

  /* GRAMASI INPUTS */
  .gram-grid{
    display:grid; grid-template-columns: repeat(5, 1fr); gap:8px; margin-top:4px;
  }
  .gram-cell{position:relative;}
  .gram-cell span.idx{
    position:absolute; top:5px; left:8px; font-size:9px; font-weight:800; color:#D9C4D2; z-index:1;
  }
  .gram-cell input{
    width:100%; text-align:center; padding:16px 4px 8px; border-radius:12px;
    border:1.5px solid var(--line); font-family:'Poppins',sans-serif; font-weight:700; font-size:16px;
    color:var(--ink); background:var(--pink-tint);
  }
  .gram-cell input:focus{ border-color:var(--pink); background:white; box-shadow:0 0 0 3px rgba(236,26,107,0.1);}
  .gram-cell input.filled{background:white; border-color:#F5C9DD;}

  /* STATS */
  .stats-row{display:flex; gap:10px; margin-top:16px;}
  .stat-box{
    flex:1; border-radius:16px; padding:14px 12px; text-align:center;
    background: linear-gradient(160deg, var(--pink-tint), #FFF7FA);
    border:1.5px solid #FBDCEA;
  }
  .stat-box.blue{
    background: linear-gradient(160deg, var(--blue-tint), #F4FCFF);
    border-color:#D5F1FA;
  }
  .stat-box .label{font-size:10.5px; font-weight:700; text-transform:uppercase; letter-spacing:.05em; color:var(--ink-soft);}
  .stat-box .value{font-family:'Poppins',sans-serif; font-weight:900; font-size:24px; margin-top:4px; color:var(--pink-deep);}
  .stat-box.blue .value{color:#1690B0;}
  .stat-box .sub{font-size:10.5px; color:var(--ink-soft); font-weight:600; margin-top:1px;}

  .progress-track{
    height:6px; background:var(--line); border-radius:10px; margin-top:14px; overflow:hidden;
  }
  .progress-fill{
    height:100%; background: linear-gradient(90deg, var(--pink), var(--blue)); border-radius:10px;
    transition: width .25s ease; width:0%;
  }
  .progress-label{font-size:11.5px; font-weight:700; color:var(--ink-soft); margin-top:6px; text-align:right;}

  /* BUTTONS */
  .btn{
    width:100%; padding:15px; border-radius:16px; border:none; font-family:'Poppins',sans-serif;
    font-weight:800; font-size:15px; cursor:pointer; display:flex; align-items:center; justify-content:center; gap:8px;
    transition: transform .1s ease, box-shadow .15s ease;
  }
  .btn:active{transform:scale(0.98);}
  .btn-primary{
    background: linear-gradient(120deg, var(--pink), var(--pink-deep)); color:white;
    box-shadow: 0 10px 20px rgba(236,26,107,0.3); margin-top:18px;
  }
  .btn-primary:disabled{ background:#EFC6D9; box-shadow:none; cursor:not-allowed; }
  .btn-secondary{
    background: var(--white); color:var(--ink); border:1.5px solid var(--line); margin-top:10px;
  }
  .btn-wa{
    background: #25D366; color:white; margin-top:10px; box-shadow: 0 10px 20px rgba(37,211,102,0.25);
  }
  .btn-ghost{
    background:transparent; color:var(--ink-soft); font-weight:700; margin-top:8px; font-size:13px; padding:8px;
  }

  /* HISTORY */
  .history-item{
    border:1.5px solid var(--line); border-radius:16px; padding:14px 16px; margin-top:10px;
    display:flex; justify-content:space-between; align-items:center; gap:10px;
  }
  .history-item .hi-left b{font-family:'Poppins',sans-serif; font-size:14.5px; font-weight:800;}
  .history-item .hi-left .meta{font-size:12px; color:var(--ink-soft); margin-top:2px; font-weight:500;}
  .history-item .hi-right{text-align:right;}
  .history-item .hi-right .avg{font-family:'Poppins',sans-serif; font-weight:900; color:var(--pink-deep); font-size:16px;}
  .history-item .hi-right .modus{font-size:11px; color:var(--ink-soft); font-weight:600;}
  .del-btn{
    background:none;border:none;color:#D3D3DC;font-size:18px;cursor:pointer;padding:4px 6px;
  }

  .empty-state{
    text-align:center; padding:26px 10px; color:var(--ink-soft); font-size:13.5px; font-weight:500;
  }

  .toast{
    position:fixed; bottom:24px; left:50%; transform:translateX(-50%) translateY(20px);
    background:var(--ink); color:white; padding:12px 20px; border-radius:100px; font-size:13.5px;
    font-weight:600; opacity:0; transition: all .25s ease; pointer-events:none; z-index:999;
    box-shadow:0 10px 30px rgba(0,0,0,0.25); white-space:nowrap;
  }
  .toast.show{opacity:1; transform:translateX(-50%) translateY(0);}

  .section-title{
    font-family:'Poppins',sans-serif; font-weight:800; font-size:16px; margin: 26px 4px 10px;
    letter-spacing:-0.01em;
  }

  textarea.preview{
    width:100%; min-height:200px; border-radius:14px; border:1.5px solid var(--line);
    padding:14px; font-family:'Inter',monospace; font-size:12.5px; color:var(--ink); resize:vertical;
    background:#FAFAFB; line-height:1.5;
  }

  .modal-bg{
    position:fixed; inset:0; background:rgba(26,26,46,0.5); z-index:100; display:none;
    align-items:flex-end; justify-content:center;
  }
  .modal-bg.show{display:flex;}
  .modal{
    background:white; width:100%; max-width:520px; border-radius: 24px 24px 0 0; padding:22px 18px 28px;
    animation: slideup .25s ease;
  }
  @keyframes slideup{from{transform:translateY(30px); opacity:0;} to{transform:translateY(0); opacity:1;}}
  .modal h3{font-family:'Poppins',sans-serif; font-weight:800; margin:0 0 12px; font-size:16px;}

  ::placeholder{color:#C7C7D1; font-weight:500;}

  @media (max-width:360px){
    .gram-grid{grid-template-columns: repeat(5,1fr); gap:6px;}
  }
</style>
</head>
<body>

<header class="top">
  <div class="brand">
    <div class="brand-mark">G</div>
    <div class="brand-text">
      <h1>Kalibrasi Gramasi</h1>
      <p>Gacoan Singosari · Shift tracker</p>
    </div>
  </div>
  <div class="datetime-row">
    <div class="dt-pill">📅 <b id="dateDisplay">--</b></div>
    <div class="dt-pill">⏰ <b id="timeDisplay">--</b></div>
  </div>
</header>

<div class="app">

  <!-- FORM CARD -->
  <div class="card">
    <h2><span class="dot"></span>Input Kalibrasi</h2>

    <label class="field-label">Nama Crew</label>
    <input type="text" id="namaCrew" placeholder="mis. Edo, Ibnu, Via...">

    <div style="margin-top:14px;">
      <label class="field-label">Item Kalibrasi</label>
      <div class="item-chips" id="itemChips"></div>
      <input type="text" id="customItem" placeholder="Atau ketik item lain..." style="margin-top:10px; display:none;">
    </div>

    <div style="margin-top:16px;">
      <label class="field-label">Shift</label>
      <div class="grid2">
        <select id="shiftSelect">
          <option value="Opening">Opening</option>
          <option value="Middle" selected>Middle</option>
          <option value="Closing">Closing</option>
        </select>
        <input type="text" id="tanggalManual" placeholder="Tanggal (auto)">
      </div>
    </div>

    <div style="margin-top:18px;">
      <label class="field-label">Gramasi (10 percobaan)</label>
      <div class="gram-grid" id="gramGrid"></div>
      <div class="progress-track"><div class="progress-fill" id="progressFill"></div></div>
      <div class="progress-label" id="progressLabel">0 / 10 terisi</div>
    </div>

    <div class="stats-row">
      <div class="stat-box">
        <div class="label">Average</div>
        <div class="value" id="avgValue">–</div>
        <div class="sub">gram</div>
      </div>
      <div class="stat-box blue">
        <div class="label">Modus</div>
        <div class="value" id="modusValue">–</div>
        <div class="sub" id="modusSub">gram</div>
      </div>
    </div>

    <button class="btn btn-primary" id="saveBtn" disabled>💾 Simpan Kalibrasi</button>
    <button class="btn btn-ghost" id="clearBtn">Bersihkan form</button>
  </div>

  <!-- HISTORY -->
  <div class="section-title">Riwayat Hari Ini</div>
  <div id="historyList">
    <div class="empty-state">Belum ada kalibrasi tersimpan hari ini.</div>
  </div>

  <button class="btn btn-wa" id="waBtn">📤 Susun Rekap & Kirim ke WhatsApp</button>
  <button class="btn btn-secondary" id="clearAllBtn">🗑️ Hapus Semua Riwayat Hari Ini</button>

</div>

<!-- PREVIEW MODAL -->
<div class="modal-bg" id="previewModal">
  <div class="modal">
    <h3>Preview Rekap WhatsApp</h3>
    <textarea class="preview" id="previewText" readonly></textarea>
    <button class="btn btn-wa" id="confirmWaBtn">✅ Buka WhatsApp</button>
    <button class="btn btn-secondary" id="copyBtn">📋 Salin Teks</button>
    <button class="btn btn-ghost" id="closeModalBtn">Tutup</button>
  </div>
</div>

<div class="toast" id="toast"></div>

<script>
(function(){
  const DEFAULT_ITEMS = ["Acin","Bagor","Basic","Minyak Mie"];
  const STORAGE_KEY = "kalibrasi_history_v1";
  const NUM_TRIALS = 10;

  let selectedItem = DEFAULT_ITEMS[0];
  let usingCustomItem = false;
  let gramValues = new Array(NUM_TRIALS).fill("");

  // ---------- date/time ----------
  function pad(n){return n.toString().padStart(2,"0");}
  function updateClock(){
    const now = new Date();
    const days = ["Minggu","Senin","Selasa","Rabu","Kamis","Jumat","Sabtu"];
    const months = ["Jan","Feb","Mar","Apr","Mei","Jun","Jul","Agu","Sep","Okt","Nov","Des"];
    document.getElementById("dateDisplay").textContent = `${days[now.getDay()]}, ${now.getDate()} ${months[now.getMonth()]} ${now.getFullYear()}`;
    document.getElementById("timeDisplay").textContent = `${pad(now.getHours())}:${pad(now.getMinutes())}`;
  }
  updateClock();
  setInterval(updateClock, 15000);

  // ---------- item chips ----------
  const chipsContainer = document.getElementById("itemChips");
  const customInput = document.getElementById("customItem");

  function renderChips(){
    chipsContainer.innerHTML = "";
    DEFAULT_ITEMS.forEach(item=>{
      const chip = document.createElement("div");
      chip.className = "chip" + (!usingCustomItem && selectedItem===item ? " active":"");
      chip.textContent = item;
      chip.onclick = ()=>{
        usingCustomItem = false;
        selectedItem = item;
        customInput.style.display = "none";
        customInput.value = "";
        renderChips();
      };
      chipsContainer.appendChild(chip);
    });
    const otherChip = document.createElement("div");
    otherChip.className = "chip" + (usingCustomItem ? " custom-active":"");
    otherChip.textContent = "+ Lainnya";
    otherChip.onclick = ()=>{
      usingCustomItem = true;
      customInput.style.display = "block";
      customInput.focus();
      renderChips();
    };
    chipsContainer.appendChild(otherChip);
  }
  customInput.addEventListener("input", ()=>{ selectedItem = customInput.value.trim(); checkFormValid(); });
  renderChips();

  // ---------- gramasi grid ----------
  const gramGrid = document.getElementById("gramGrid");
  function renderGramGrid(){
    gramGrid.innerHTML = "";
    for(let i=0;i<NUM_TRIALS;i++){
      const cell = document.createElement("div");
      cell.className = "gram-cell";
      cell.innerHTML = `<span class="idx">${i+1}</span><input type="text" inputmode="decimal" data-idx="${i}" placeholder="–">`;
      gramGrid.appendChild(cell);
    }
    gramGrid.querySelectorAll("input").forEach(inp=>{
      inp.addEventListener("input", (e)=>{
        let v = e.target.value.replace(",",".").replace(/[^0-9.]/g,"");
        e.target.value = v;
        const idx = parseInt(e.target.dataset.idx);
        gramValues[idx] = v;
        e.target.classList.toggle("filled", v !== "");
        computeStats();
        // auto-advance
        if(v !== "" && idx < NUM_TRIALS-1){
          const next = gramGrid.querySelector(`input[data-idx="${idx+1}"]`);
          if(next && next.value === "") { /* let user tab manually via next key; keep simple */ }
        }
      });
      inp.addEventListener("keydown",(e)=>{
        const idx = parseInt(e.target.dataset.idx);
        if(e.key === "Enter" || e.key === "Tab"){
          e.preventDefault();
          const next = gramGrid.querySelector(`input[data-idx="${idx+1}"]`);
          if(next) next.focus(); else e.target.blur();
        }
      });
    });
  }
  renderGramGrid();

  function computeStats(){
    const nums = gramValues.filter(v=>v!=="" && !isNaN(parseFloat(v))).map(v=>parseFloat(v));
    const filledCount = nums.length;
    document.getElementById("progressLabel").textContent = `${filledCount} / ${NUM_TRIALS} terisi`;
    document.getElementById("progressFill").style.width = `${(filledCount/NUM_TRIALS)*100}%`;

    if(filledCount === 0){
      document.getElementById("avgValue").textContent = "–";
      document.getElementById("modusValue").textContent = "–";
      document.getElementById("modusSub").textContent = "gram";
    } else {
      const avg = nums.reduce((a,b)=>a+b,0) / filledCount;
      document.getElementById("avgValue").textContent = avg.toFixed(2);

      const freq = {};
      nums.forEach(n=>{ freq[n] = (freq[n]||0)+1; });
      let maxFreq = 0;
      Object.values(freq).forEach(f=>{ if(f>maxFreq) maxFreq=f; });
      const modes = Object.keys(freq).filter(k=>freq[k]===maxFreq).map(Number).sort((a,b)=>a-b);
      document.getElementById("modusValue").textContent = modes.join(", ");
      document.getElementById("modusSub").textContent = `gram (${maxFreq}x)`;
    }
    checkFormValid();
  }

  function checkFormValid(){
    const nama = document.getElementById("namaCrew").value.trim();
    const filledCount = gramValues.filter(v=>v!=="").length;
    const itemOk = usingCustomItem ? customInput.value.trim().length>0 : true;
    document.getElementById("saveBtn").disabled = !(nama.length>0 && filledCount===NUM_TRIALS && itemOk);
  }
  document.getElementById("namaCrew").addEventListener("input", checkFormValid);

  // ---------- storage ----------
  function loadHistory(){
    try{
      const raw = localStorage.getItem(STORAGE_KEY);
      return raw ? JSON.parse(raw) : [];
    }catch(e){ return []; }
  }
  function saveHistory(list){
    localStorage.setItem(STORAGE_KEY, JSON.stringify(list));
  }
  function todayKey(){
    const now = new Date();
    return `${now.getFullYear()}-${pad(now.getMonth()+1)}-${pad(now.getDate())}`;
  }

  function renderHistory(){
    const all = loadHistory();
    const today = todayKey();
    const todays = all.filter(h=>h.dayKey===today);
    const list = document.getElementById("historyList");
    if(todays.length===0){
      list.innerHTML = '<div class="empty-state">Belum ada kalibrasi tersimpan hari ini.</div>';
      return;
    }
    list.innerHTML = "";
    todays.slice().reverse().forEach(h=>{
      const div = document.createElement("div");
      div.className = "history-item";
      div.innerHTML = `
        <div class="hi-left">
          <b>${escapeHtml(h.nama)}</b>
          <div class="meta">${escapeHtml(h.item)} · ${h.shift} · ${h.time}</div>
        </div>
        <div class="hi-right">
          <div class="avg">${h.avg} g</div>
          <div class="modus">Modus ${h.modus} (${h.modusFreq}x)</div>
        </div>
        <button class="del-btn" data-id="${h.id}">✕</button>
      `;
      list.appendChild(div);
    });
    list.querySelectorAll(".del-btn").forEach(btn=>{
      btn.onclick = ()=>{
        const id = btn.dataset.id;
        let all = loadHistory();
        all = all.filter(h=>h.id !== id);
        saveHistory(all);
        renderHistory();
        showToast("Data dihapus");
      };
    });
  }

  function escapeHtml(str){
    const div = document.createElement("div");
    div.textContent = str;
    return div.innerHTML;
  }

  // ---------- save ----------
  document.getElementById("saveBtn").addEventListener("click", ()=>{
    const nama = document.getElementById("namaCrew").value.trim();
    const item = usingCustomItem ? customInput.value.trim() : selectedItem;
    const shift = document.getElementById("shiftSelect").value;
    const nums = gramValues.map(v=>parseFloat(v));
    const avg = (nums.reduce((a,b)=>a+b,0)/nums.length).toFixed(2);
    const freq = {};
    nums.forEach(n=>{ freq[n]=(freq[n]||0)+1; });
    let maxFreq=0;
    Object.values(freq).forEach(f=>{ if(f>maxFreq) maxFreq=f; });
    const modes = Object.keys(freq).filter(k=>freq[k]===maxFreq).map(Number).sort((a,b)=>a-b).join(", ");

    const now = new Date();
    const entry = {
      id: Date.now().toString(36) + Math.random().toString(36).slice(2,6),
      nama, item, shift,
      data: nums.slice(),
      avg, modus: modes, modusFreq: maxFreq,
      time: `${pad(now.getHours())}:${pad(now.getMinutes())}`,
      dayKey: todayKey(),
      dateLabel: document.getElementById("dateDisplay").textContent
    };
    const all = loadHistory();
    all.push(entry);
    saveHistory(all);
    renderHistory();
    showToast(`Kalibrasi ${nama} tersimpan ✓`);

    // reset form
    document.getElementById("namaCrew").value = "";
    gramValues = new Array(NUM_TRIALS).fill("");
    renderGramGrid();
    computeStats();
  });

  document.getElementById("clearBtn").addEventListener("click", ()=>{
    document.getElementById("namaCrew").value = "";
    customInput.value = "";
    usingCustomItem = false;
    customInput.style.display = "none";
    selectedItem = DEFAULT_ITEMS[0];
    renderChips();
    gramValues = new Array(NUM_TRIALS).fill("");
    renderGramGrid();
    computeStats();
  });

  document.getElementById("clearAllBtn").addEventListener("click", ()=>{
    if(!confirm("Hapus semua riwayat kalibrasi hari ini? Tindakan ini tidak bisa dibatalkan.")) return;
    const all = loadHistory();
    const today = todayKey();
    const remaining = all.filter(h=>h.dayKey!==today);
    saveHistory(remaining);
    renderHistory();
    showToast("Riwayat hari ini dihapus");
  });

  // ---------- WA recap ----------
  function buildRecapText(){
    const all = loadHistory();
    const today = todayKey();
    const todays = all.filter(h=>h.dayKey===today);
    if(todays.length===0) return null;

    const shift = todays[todays.length-1].shift;
    const dateLabel = todays[todays.length-1].dateLabel;
    let text = `Kalibrasi Shift ${shift} - ${dateLabel}\n\n`;

    // group by nama, preserving order of first appearance
    const grouped = {};
    const order = [];
    todays.forEach(h=>{
      if(!grouped[h.nama]){ grouped[h.nama]=[]; order.push(h.nama); }
      grouped[h.nama].push(h);
    });

    order.forEach(nama=>{
      text += `${nama} :\n`;
      grouped[nama].forEach(h=>{
        text += `• ${h.item} - Data: ${h.data.join(", ")}\n`;
        text += `• Average: ${h.avg} gram\n`;
        text += `• Modus: ${h.modus} gram (${h.modusFreq}x)\n`;
      });
      text += `\n`;
    });

    return text.trim();
  }

  document.getElementById("waBtn").addEventListener("click", ()=>{
    const text = buildRecapText();
    if(!text){
      showToast("Belum ada data untuk direkap");
      return;
    }
    document.getElementById("previewText").value = text;
    document.getElementById("previewModal").classList.add("show");
  });

  document.getElementById("closeModalBtn").addEventListener("click", ()=>{
    document.getElementById("previewModal").classList.remove("show");
  });

  document.getElementById("copyBtn").addEventListener("click", ()=>{
    const ta = document.getElementById("previewText");
    ta.select();
    document.execCommand("copy");
    showToast("Teks disalin ✓");
  });

  document.getElementById("confirmWaBtn").addEventListener("click", ()=>{
    const text = document.getElementById("previewText").value;
    const url = `https://wa.me/?text=${encodeURIComponent(text)}`;
    window.open(url, "_blank");
  });

  // ---------- toast ----------
  let toastTimer;
  function showToast(msg){
    const t = document.getElementById("toast");
    t.textContent = msg;
    t.classList.add("show");
    clearTimeout(toastTimer);
    toastTimer = setTimeout(()=>t.classList.remove("show"), 2200);
  }

  // init
  document.getElementById("tanggalManual").value = "";
  document.getElementById("tanggalManual").disabled = true;
  document.getElementById("tanggalManual").placeholder = document.getElementById("dateDisplay").textContent;
  renderHistory();
  computeStats();
})();
</script>

</body>
</html>
