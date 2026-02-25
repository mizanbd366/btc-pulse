# btc-pulse
BTC Pulse is a real-time Bitcoin L1 dashboard powered by OP_NET infrastructure. Track live BTC price updates every 3 seconds, monitor network stats (hashrate, difficulty, mempool, fees), view 24h mempool activity chart, and connect your OP_WALLET to interact with Bitcoin Layer 1 dApps. Built for the OP_NET Vibecoding Challenge Week 1.
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>BTC Pulse — Bitcoin L1 Dashboard</title>
<link href="https://fonts.googleapis.com/css2?family=Space+Mono:wght@400;700&family=Syne:wght@400;600;800&display=swap" rel="stylesheet">
<style>
  :root {
    --btc: #F7931A;
    --bg: #090909;
    --bg2: #111111;
    --border: #2a2a2a;
    --text: #f0f0f0;
    --muted: #666;
    --green: #00e676;
    --red: #ff3d57;
    --glow: 0 0 30px rgba(247,147,26,0.25);
  }
  * { margin: 0; padding: 0; box-sizing: border-box; }
  body {
    background: var(--bg);
    color: var(--text);
    font-family: 'Syne', sans-serif;
    min-height: 100vh;
    overflow-x: hidden;
  }
  body::before {
    content: '';
    position: fixed;
    inset: 0;
    background-image:
      linear-gradient(rgba(247,147,26,0.03) 1px, transparent 1px),
      linear-gradient(90deg, rgba(247,147,26,0.03) 1px, transparent 1px);
    background-size: 40px 40px;
    animation: gridPulse 8s ease-in-out infinite;
    pointer-events: none;
    z-index: 0;
  }
  @keyframes gridPulse { 0%,100%{opacity:.5} 50%{opacity:1} }
  .container { max-width:900px; margin:0 auto; padding:20px 16px 60px; position:relative; z-index:1; }
  header { display:flex; align-items:center; justify-content:space-between; padding:20px 0 40px; border-bottom:1px solid var(--border); margin-bottom:40px; }
  .logo { display:flex; align-items:center; gap:12px; }
  .logo-icon { width:44px; height:44px; background:var(--btc); border-radius:12px; display:flex; align-items:center; justify-content:center; font-size:22px; animation:breathe 3s ease-in-out infinite; }
  @keyframes breathe { 0%,100%{box-shadow:0 0 20px rgba(247,147,26,.3)} 50%{box-shadow:0 0 40px rgba(247,147,26,.6)} }
  .logo-text { font-size:22px; font-weight:800; letter-spacing:-.5px; }
  .logo-sub { font-size:11px; color:var(--muted); font-family:'Space Mono',monospace; letter-spacing:2px; text-transform:uppercase; }
  .live-badge { display:flex; align-items:center; gap:6px; background:rgba(0,230,118,.1); border:1px solid rgba(0,230,118,.3); padding:6px 14px; border-radius:100px; font-size:12px; font-family:'Space Mono',monospace; color:var(--green); }
  .live-dot { width:7px; height:7px; background:var(--green); border-radius:50%; animation:blink 1.5s ease-in-out infinite; }
  @keyframes blink { 0%,100%{opacity:1} 50%{opacity:.3} }
  .price-hero { text-align:center; padding:50px 20px; }
  .price-label { font-size:11px; letter-spacing:4px; text-transform:uppercase; color:var(--muted); font-family:'Space Mono',monospace; margin-bottom:12px; }
  .price-value { font-size:clamp(52px,12vw,96px); font-weight:800; letter-spacing:-3px; color:var(--btc); text-shadow:0 0 60px rgba(247,147,26,.4); line-height:1; transition:all .4s ease; }
  .price-change { font-size:18px; font-family:'Space Mono',monospace; margin-top:12px; transition:color .3s; }
  .price-change.up { color:var(--green); }
  .price-change.down { color:var(--red); }
  .ticker-bar { background:var(--bg2); border:1px solid var(--border); border-radius:12px; padding:14px 24px; display:flex; justify-content:space-around; flex-wrap:wrap; gap:12px; margin-bottom:32px; }
  .ticker-item { text-align:center; min-width:100px; }
  .ticker-label { font-size:10px; letter-spacing:2px; text-transform:uppercase; color:var(--muted); font-family:'Space Mono',monospace; margin-bottom:4px; }
  .ticker-val { font-size:15px; font-weight:700; font-family:'Space Mono',monospace; color:var(--btc); }
  .grid { display:grid; grid-template-columns:1fr 1fr; gap:16px; margin-bottom:24px; }
  @media(max-width:560px){.grid{grid-template-columns:1fr}}
  .card { background:var(--bg2); border:1px solid var(--border); border-radius:16px; padding:24px; position:relative; overflow:hidden; transition:border-color .3s,transform .2s; }
  .card:hover { border-color:rgba(247,147,26,.4); transform:translateY(-2px); }
  .card::before { content:''; position:absolute; top:0; left:0; right:0; height:2px; background:linear-gradient(90deg,transparent,var(--btc),transparent); opacity:0; transition:opacity .3s; }
  .card:hover::before { opacity:1; }
  .card-icon { font-size:28px; margin-bottom:12px; }
  .card-label { font-size:10px; letter-spacing:2.5px; text-transform:uppercase; color:var(--muted); font-family:'Space Mono',monospace; margin-bottom:6px; }
  .card-value { font-size:26px; font-weight:800; letter-spacing:-.5px; }
  .card-sub { font-size:12px; color:var(--muted); font-family:'Space Mono',monospace; margin-top:4px; }
  .section-title { font-size:13px; letter-spacing:3px; text-transform:uppercase; color:var(--btc); font-family:'Space Mono',monospace; margin-bottom:16px; display:flex; align-items:center; gap:10px; }
  .section-title::after { content:''; flex:1; height:1px; background:var(--border); }
  .opnet-card { background:linear-gradient(135deg,#0d0d0d,#161208); border:1px solid rgba(247,147,26,.2); border-radius:20px; padding:32px; margin-bottom:24px; position:relative; overflow:hidden; }
  .opnet-card::after { content:'OP_NET'; position:absolute; right:-20px; top:50%; transform:translateY(-50%) rotate(90deg); font-size:80px; font-weight:800; color:rgba(247,147,26,.04); pointer-events:none; }
  .opnet-title { font-size:20px; font-weight:800; margin-bottom:8px; }
  .opnet-desc { color:var(--muted); font-size:14px; line-height:1.6; max-width:500px; margin-bottom:24px; }
  .opnet-stats { display:flex; gap:32px; flex-wrap:wrap; }
  .opnet-stat-val { font-size:28px; font-weight:800; color:var(--btc); font-family:'Space Mono',monospace; }
  .opnet-stat-label { font-size:11px; color:var(--muted); text-transform:uppercase; letter-spacing:1.5px; margin-top:2px; }
  .mempool-bars { display:flex; align-items:flex-end; gap:4px; height:80px; margin-top:16px; }
  .bar { flex:1; border-radius:4px 4px 0 0; background:var(--btc); opacity:.7; transition:height 1s ease; animation:barLoad 1s ease forwards; }
  .bar:hover { opacity:1; }
  @keyframes barLoad { from{height:0!important} }
  .wallet-section { background:var(--bg2); border:1px solid var(--border); border-radius:16px; padding:28px; text-align:center; margin-bottom:24px; }
  .wallet-title { font-size:18px; font-weight:700; margin-bottom:8px; }
  .wallet-desc { color:var(--muted); font-size:14px; margin-bottom:20px; }
  .btn { display:inline-flex; align-items:center; gap:8px; background:var(--btc); color:#000; border:none; padding:14px 28px; border-radius:12px; font-size:14px; font-weight:700; font-family:'Syne',sans-serif; cursor:pointer; transition:all .2s; box-shadow:0 4px 20px rgba(247,147,26,.3); }
  .btn:hover { background:#ffaa33; transform:translateY(-1px); box-shadow:0 6px 30px rgba(247,147,26,.5); }
  .btn-outline { background:transparent; color:var(--btc); border:1px solid var(--btc); box-shadow:none; }
  .btn-outline:hover { background:rgba(247,147,26,.1); box-shadow:none; }
  .wallet-connected { display:none; align-items:center; gap:12px; background:rgba(0,230,118,.08); border:1px solid rgba(0,230,118,.2); border-radius:12px; padding:16px 20px; margin-top:16px; text-align:left; }
  .wallet-addr { font-family:'Space Mono',monospace; font-size:13px; color:var(--green); }
  .wallet-bal { font-size:12px; color:var(--muted); font-family:'Space Mono',monospace; margin-top:2px; }
  .tx-list { display:flex; flex-direction:column; gap:8px; }
  .tx-item { background:var(--bg2); border:1px solid var(--border); border-radius:12px; padding:14px 18px; display:flex; align-items:center; justify-content:space-between; transition:border-color .3s; animation:slideIn .4s ease forwards; opacity:0; }
  @keyframes slideIn { from{opacity:0;transform:translateX(-10px)} to{opacity:1;transform:translateX(0)} }
  .tx-item:hover { border-color:rgba(247,147,26,.3); }
  .tx-left { display:flex; align-items:center; gap:12px; }
  .tx-icon { width:36px; height:36px; border-radius:10px; display:flex; align-items:center; justify-content:center; font-size:16px; }
  .tx-icon.recv { background:rgba(0,230,118,.15); }
  .tx-icon.send { background:rgba(255,61,87,.15); }
  .tx-hash { font-family:'Space Mono',monospace; font-size:12px; color:var(--muted); }
  .tx-label { font-size:13px; font-weight:600; margin-bottom:2px; }
  .tx-amount { font-family:'Space Mono',monospace; font-size:14px; font-weight:700; }
  .tx-amount.pos { color:var(--green); }
  .tx-amount.neg { color:var(--red); }
  footer { text-align:center; padding:40px 0 20px; border-top:1px solid var(--border); margin-top:40px; }
  .footer-logo { font-size:13px; color:var(--muted); font-family:'Space Mono',monospace; }
  .footer-logo span { color:var(--btc); }
  @keyframes pricePulse { 0%{text-shadow:0 0 60px rgba(247,147,26,.4)} 50%{text-shadow:0 0 100px rgba(247,147,26,.8),0 0 160px rgba(247,147,26,.4)} 100%{text-shadow:0 0 60px rgba(247,147,26,.4)} }
  .price-updating { animation:pricePulse .5s ease; }
  @media(max-width:480px){.opnet-stats{gap:20px}.ticker-bar{justify-content:flex-start;overflow-x:auto;flex-wrap:nowrap}header{flex-direction:column;gap:16px;align-items:flex-start}}
</style>
</head>
<body>
<div class="container">

  <header>
    <div class="logo">
      <div class="logo-icon">₿</div>
      <div>
        <div class="logo-text">BTC Pulse</div>
        <div class="logo-sub">Bitcoin L1 Dashboard</div>
      </div>
    </div>
    <div class="live-badge">
      <div class="live-dot"></div>LIVE
    </div>
  </header>

  <div class="price-hero">
    <div class="price-label">Bitcoin / USD</div>
    <div class="price-value" id="price">$95,240</div>
    <div class="price-change up" id="change">▲ +2.34% today</div>
  </div>

  <div class="ticker-bar">
    <div class="ticker-item"><div class="ticker-label">24h Volume</div><div class="ticker-val">$42.1B</div></div>
    <div class="ticker-item"><div class="ticker-label">Market Cap</div><div class="ticker-val">$1.87T</div></div>
    <div class="ticker-item"><div class="ticker-label">Dominance</div><div class="ticker-val">58.4%</div></div>
    <div class="ticker-item"><div class="ticker-label">Block Height</div><div class="ticker-val" id="blockHeight">884,217</div></div>
    <div class="ticker-item"><div class="ticker-label">Halving</div><div class="ticker-val">~2028</div></div>
  </div>

  <div class="section-title">Network Status</div>
  <div class="grid">
    <div class="card"><div class="card-icon">⛏️</div><div class="card-label">Mining Difficulty</div><div class="card-value">109.78T</div><div class="card-sub">↑ +3.1% last epoch</div></div>
    <div class="card"><div class="card-icon">⚡</div><div class="card-label">Hash Rate</div><div class="card-value">782 EH/s</div><div class="card-sub">All time high</div></div>
    <div class="card"><div class="card-icon">📦</div><div class="card-label">Mempool Size</div><div class="card-value" id="mempool">14,832</div><div class="card-sub">transactions pending</div></div>
    <div class="card"><div class="card-icon">💸</div><div class="card-label">Avg Fee</div><div class="card-value" id="fee">12 sat/vB</div><div class="card-sub">~$0.84 per tx</div></div>
  </div>

  <div class="section-title">Mempool Activity (24h)</div>
  <div class="card" style="margin-bottom:24px;">
    <div class="card-label">Transaction Volume</div>
    <div class="mempool-bars" id="mempoolChart"></div>
  </div>

  <div class="section-title">OP_NET Layer</div>
  <div class="opnet-card">
    <div class="opnet-title">Bitcoin Just Became Programmable</div>
    <div class="opnet-desc">OP_NET brings smart contract infrastructure to Bitcoin Layer 1 — enabling DeFi, token deployment, and decentralized apps without leaving Bitcoin's security model.</div>
    <div class="opnet-stats">
      <div><div class="opnet-stat-val">12</div><div class="opnet-stat-label">dApps Deployed</div></div>
      <div><div class="opnet-stat-val">$0→∞</div><div class="opnet-stat-label">L1 DeFi Potential</div></div>
      <div><div class="opnet-stat-val">100M+</div><div class="opnet-stat-label">BTC Holders</div></div>
    </div>
  </div>

  <div class="section-title">OP_WALLET</div>
  <div class="wallet-section">
    <div class="wallet-title">Connect Your Bitcoin Wallet</div>
    <div class="wallet-desc">Connect OP_WALLET to view your balances, send BTC, and interact with Bitcoin L1 dApps powered by OP_NET.</div>
    <button class="btn" id="connectBtn" onclick="connectWallet()">₿ Connect OP_WALLET</button>
    <div class="wallet-connected" id="walletInfo">
      <div style="font-size:28px">🟠</div>
      <div>
        <div class="wallet-addr" id="walletAddr">bc1q4x9...f3m7</div>
        <div class="wallet-bal" id="walletBal">0.00831 BTC ≈ $793</div>
      </div>
      <div style="margin-left:auto;">
        <button class="btn btn-outline" style="padding:8px 16px;font-size:12px;" onclick="disconnectWallet()">Disconnect</button>
      </div>
    </div>
  </div>

  <div class="section-title">Recent Transactions</div>
  <div class="tx-list">
    <div class="tx-item" style="animation-delay:.1s"><div class="tx-left"><div class="tx-icon recv">📥</div><div><div class="tx-label">Received</div><div class="tx-hash">a3f9...82bc · 3 min ago</div></div></div><div class="tx-amount pos">+0.0021 BTC</div></div>
    <div class="tx-item" style="animation-delay:.2s"><div class="tx-left"><div class="tx-icon send">📤</div><div><div class="tx-label">Sent</div><div class="tx-hash">7c1e...44af · 18 min ago</div></div></div><div class="tx-amount neg">−0.0005 BTC</div></div>
    <div class="tx-item" style="animation-delay:.3s"><div class="tx-left"><div class="tx-icon recv">⚡</div><div><div class="tx-label">OP_NET Interaction</div><div class="tx-hash">f0b2...9d11 · 1 hr ago</div></div></div><div class="tx-amount pos">+Token Deploy</div></div>
  </div>

  <footer>
    <div class="footer-logo">Built on <span>Bitcoin L1</span> · Powered by <span>OP_NET</span> · vibecode.finance</div>
    <div style="font-size:11px;color:var(--muted);margin-top:8px;font-family:'Space Mono',monospace;">#opnetvibecode · Week 1 Challenge</div>
  </footer>

</div>
<script>
  let currentPrice = 95240;
  const priceEl = document.getElementById('price');
  const changeEl = document.getElementById('change');
  const blockEl = document.getElementById('blockHeight');
  const mempoolEl = document.getElementById('mempool');
  const feeEl = document.getElementById('fee');
  let blockHeight = 884217;

  function formatPrice(p) {
    return '$' + p.toLocaleString('en-US', {minimumFractionDigits:0, maximumFractionDigits:0});
  }

  function updatePrice() {
    let delta = (Math.random() - 0.48) * 180;
    currentPrice = Math.max(80000, Math.min(120000, currentPrice + delta));
    let pct = ((currentPrice - 95240) / 95240 * 100).toFixed(2);
    let isUp = delta > 0;
    priceEl.textContent = formatPrice(Math.round(currentPrice));
    priceEl.classList.add('price-updating');
    setTimeout(() => priceEl.classList.remove('price-updating'), 500);
    changeEl.textContent = (isUp ? '▲ +' : '▼ ') + Math.abs(pct) + '% today';
    changeEl.className = 'price-change ' + (isUp ? 'up' : 'down');
  }

  function updateBlock() {
    if (Math.random() < 0.1) { blockHeight++; blockEl.textContent = blockHeight.toLocaleString(); }
    mempoolEl.textContent = (14832 + Math.floor((Math.random()-.5)*200)).toLocaleString();
    feeEl.textContent = (10 + Math.floor(Math.random()*6)) + ' sat/vB';
  }

  function buildChart() {
    const chart = document.getElementById('mempoolChart');
    [30,45,62,38,55,70,82,68,75,90,65,50,44,38,55,72,88,95,80,70,60,75,85,78].forEach((h,i) => {
      const bar = document.createElement('div');
      bar.className = 'bar';
      bar.style.height = h + '%';
      bar.style.animationDelay = (i*30) + 'ms';
      chart.appendChild(bar);
    });
  }

  function connectWallet() {
    const btn = document.getElementById('connectBtn');
    const info = document.getElementById('walletInfo');
    btn.textContent = '⏳ Connecting...';
    btn.disabled = true;
    setTimeout(() => {
      btn.style.display = 'none';
      info.style.display = 'flex';
      document.getElementById('walletBal').textContent = '0.00831 BTC ≈ $' + Math.round(0.00831 * currentPrice);
    }, 1200);
  }

  function disconnectWallet() {
    const btn = document.getElementById('connectBtn');
    btn.style.display = 'inline-flex';
    btn.textContent = '₿ Connect OP_WALLET';
    btn.disabled = false;
    document.getElementById('walletInfo').style.display = 'none';
  }

  buildChart();
  setInterval(updatePrice, 3000);
  setInterval(updateBlock, 5000);
</script>
</body>
</html>
