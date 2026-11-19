# Market.scalping-<!doctype html>
<html lang="en">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<meta name="theme-color" content="#08111f">
<title>Market Scalper | Deriv Digit Scanner</title>
<style>
:root{color-scheme:dark;--bg:#08111f;--panel:#101d30;--line:#20334d;--muted:#91a4bd;--text:#edf4ff;--green:#35d49a;--red:#ff6b78;--blue:#72a7ff}
*{box-sizing:border-box}body{margin:0;background:radial-gradient(circle at top,#132844,var(--bg) 55%);color:var(--text);font:15px system-ui,-apple-system,Segoe UI,sans-serif}
main{max-width:1050px;margin:auto;padding:22px 16px 50px}.top{display:flex;justify-content:space-between;align-items:center;gap:12px;flex-wrap:wrap;margin-bottom:20px}
h1{font-size:clamp(25px,5vw,36px);margin:0;letter-spacing:-1px}.sub{color:var(--muted);margin-top:5px}.status{border:1px solid var(--line);border-radius:30px;padding:8px 12px;color:var(--muted);font-size:13px}.dot{display:inline-block;width:8px;height:8px;border-radius:50%;background:#77869a;margin-right:7px}.connected .dot{background:var(--green)}
.grid{display:grid;grid-template-columns:repeat(2,minmax(0,1fr));gap:14px}.panel{background:linear-gradient(145deg,#13233a,#0e192a);border:1px solid var(--line);border-radius:17px;padding:17px;min-width:0}.wide{grid-column:1/-1}
label{display:block;color:var(--muted);font-size:12px;margin-bottom:7px}select,button{font:inherit;color:var(--text);background:#0a1728;border:1px solid #2b4160;border-radius:10px;padding:11px;width:100%}button{cursor:pointer;font-weight:650;background:#1d3c62}button:disabled{opacity:.5;cursor:not-allowed}.controls{display:grid;grid-template-columns:repeat(3,minmax(0,1fr));gap:10px}
.stats{display:grid;grid-template-columns:repeat(3,1fr);gap:10px}.stat{background:#0a1728;border-radius:12px;padding:13px}.stat strong{display:block;font-size:25px;margin-top:4px}.muted{color:var(--muted);font-size:12px}
.modes{display:grid;grid-template-columns:repeat(6,minmax(0,1fr));gap:8px}.mode{padding:10px 4px;font-size:12px}.mode.active{border-color:var(--blue);background:#1c3c62;color:white}
.signal{font-size:clamp(26px,6vw,40px);font-weight:800;letter-spacing:1px;margin:4px 0}.signal.wait{color:#ffd27a}.signal.go{color:var(--green)}.signal.bad{color:var(--red)}
.ticks{display:flex;gap:7px;flex-wrap:wrap;margin-top:12px}.digit{width:35px;height:39px;border:1px solid var(--line);border-radius:9px;display:grid;place-items:center;background:#0a1728;font-weight:700}.digit.latest{border-color:var(--green);color:var(--green)}
.bar{height:8px;border-radius:10px;background:#233650;overflow:hidden;margin-top:10px}.bar span{display:block;height:100%;background:var(--green);width:0%}
#log{max-height:180px;overflow:auto;font:12px ui-monospace,monospace;color:var(--muted);line-height:1.8}
.note{font-size:12px;line-height:1.6;color:var(--muted);margin-top:15px}
@media(max-width:650px){.grid{grid-template-columns:1fr}.wide{grid-column:auto}.controls{grid-template-columns:1fr 1fr}.modes{grid-template-columns:repeat(3,1fr)}.stats{grid-template-columns:repeat(3,1fr)}}
</style>
</head>
<body>
<main>
<header class="top">
  <div><h1>MARKET <span style="color:var(--green)">SCALPER</span></h1><div class="sub">Deriv digit scanner · live tick analysis</div></div>
  <div id="status" class="status"><i class="dot"></i><span id="statusText">Disconnected</span></div>
</header>
<div class="grid">
<section class="panel wide">
  <div class="controls">
    <div><label for="market">SYNTHETIC MARKET</label><select id="market">
      <option>R_10</option><option>R_25</option><option>R_50</option><option>R_75</option><option selected>R_100</option>
      <option>1HZ10V</option><option>1HZ25V</option><option>1HZ50V</option><option>1HZ75V</option><option>1HZ100V</option>
    </select></div>
    <div><label for="window">ANALYSIS WINDOW</label><select id="window">
      <option value="30">Last 30 ticks</option><option value="60">Last 60 ticks</option><option value="100" selected>Last 100 ticks</option><option value="200">Last 200 ticks</option>
    </select></div>
    <div style="display:flex;align-items:end"><button id="connect">Connect live ticks</button></div>
  </div>
</section>
<section class="panel wide">
  <label>SCANNER MODE</label>
  <div class="modes" id="modes">
    <button class="mode active" data-mode="EVEN">EVEN</button><button class="mode" data-mode="ODD">ODD</button>
    <button class="mode" data-mode="OVER">OVER</button><button class="mode" data-mode="UNDER">UNDER</button>
    <button class="mode" data-mode="MATCHES">MATCHES</button><button class="mode" data-mode="DIFFERS">DIFFERS</button>
  </div>
  <div id="barrierWrap" style="display:none;max-width:180px;margin-top:13px">
    <label for="barrier">BARRIER / TARGET DIGIT</label><select id="barrier">
      <option>0</option><option>1</option><option>2</option><option>3</option><option>4</option>
      <option>5</option><option>6</option><option>7</option><option>8</option><option>9</option>
    </select>
  </div>
</section>
<section class="panel">
  <label>CURRENT SIGNAL</label><div id="signal" class="signal wait">WAIT</div>
  <div id="signalInfo" class="muted">Connect to Deriv to begin collecting ticks.</div>
  <div class="bar"><span id="strength"></span></div>
  <div class="muted" style="margin-top:8px">Observed frequency · not a prediction</div>
</section>
<section class="panel">
  <label>LIVE MARKET</label>
  <div class="stats">
    <div class="stat"><span class="muted">Last digit</span><strong id="last">–</strong></div>
    <div class="stat"><span class="muted">Ticks</span><strong id="count">0</strong></div>
    <div class="stat"><span class="muted">Window</span><strong id="windowLabel">100</strong></div>
  </div>
  <div class="muted" style="margin-top:12px">Last quote: <span id="quote">–</span></div>
</section>
<section class="panel wide">
  <label>DIGIT DISTRIBUTION (0–9)</label><div class="stats" id="distribution"></div><div class="ticks" id="recent"></div>
</section>
<section class="panel wide"><label>CONNECTION LOG</label><div id="log">Ready. Connect to start.</div></section>
</div>
<p class="note">For demo analysis only. Signals are simple summaries of recent digit frequency, not AI predictions and not evidence of a trading edge. Random market outcomes can change immediately. No trades are placed by this website. Use a Deriv demo account and never risk money based on this scanner alone.</p>
</main>
<script>
const $=id=>document.getElementById(id);
let ws=null,ticks=[],mode='EVEN',connected=false;
function log(s){$('log').textContent=new Date().toLocaleTimeString()+'  '+s+'\n'+$('log').textContent;$('log').textContent=$('log').textContent.slice(0,1800)}
function setStatus(s,ok=false){$('statusText').textContent=s;$('status').classList.toggle('connected',ok)}
function lastDigit(q){
  const str=String(q), decimals=(str.split('.')[1]||'');
  return Number((decimals.padEnd(2,'0').slice(-1))||str.slice(-1));
}
function selected(d){
  const b=Number($('barrier').value);
  switch(mode){
    case'EVEN':return d%2===0;case'ODD':return d%2===1;
    case'OVER':return d>b;case'UNDER':return d<b;
    case'MATCHES':return d===b;case'DIFFERS':return d!==b;
  }
}
function render(){
  const n=Number($('window').value),a=ticks.slice(-n),counts=Array(10).fill(0);
  a.forEach(x=>counts[x.d]++);
  $('count').textContent=ticks.length;$('windowLabel').textContent=n;
  $('last').textContent=a.length?a[a.length-1].d:'–';$('quote').textContent=a.length?a[a.length-1].q:'–';
  $('distribution').innerHTML=counts.map((c,i)=>'<div class="stat"><span class="muted">Digit '+i+'</span><strong>'+c+'</strong><div class="muted">'+(a.length?(c/a.length*100).toFixed(1):'0.0')+'%</div></div>').join('');
  $('recent').innerHTML=a.slice(-25).map((x,i)=>'<span class="digit '+(i===Math.min(24,a.length-1)?'latest':'')+'">'+x.d+'</span>').join('');
  const hits=a.filter(x=>selected(x.d)).length,p=a.length?hits/a.length:0;
  const threshold=mode==='MATCHES'?0.2:mode==='DIFFERS'?0.8:0.6;
  let label='WAIT',cls='wait',info='Need at least 30 ticks before analysis.';
  if(a.length>=30){
    if(p>=threshold){label=mode+' BIAS';cls='go';info=hits+'/'+a.length+' recent ticks matched ('+(p*100).toFixed(1)+'%).';}
    else{label='NO CLEAR BIAS';info=hits+'/'+a.length+' recent ticks matched ('+(p*100).toFixed(1)+'%).';}
  }
  $('signal').textContent=label;$('signal').className='signal '+cls;$('signalInfo').textContent=info;$('strength').style.width=(p*100)+'%';
}
function disconnect(){
  if(ws){try{ws.close()}catch(e){}ws=null}
  connected=false;setStatus('Disconnected');$('connect').textContent='Connect live ticks';
}
function connect(){
  disconnect();ticks=[];render();const symbol=$('market').value;
  setStatus('Connecting…');$('connect').textContent='Connecting…';
  ws=new WebSocket('wss://ws.derivws.com/websockets/v3?app_id=1089');
  ws.onopen=()=>{
    connected=true;setStatus('Connected · '+symbol,true);$('connect').textContent='Disconnect';
    ws.send(JSON.stringify({ticks:symbol,subscribe:1}));log('Connected to '+symbol);
  };
  ws.onmessage=e=>{
    const m=JSON.parse(e.data);
    if(m.error){log('Error: '+m.error.message);setStatus('API error');return}
    if(m.msg_type==='tick'&&m.tick){
      const q=m.tick.quote,d=lastDigit(q);ticks.push({d,q:String(q)});
      if(ticks.length>1000)ticks.shift();render();
    }
  };
  ws.onerror=()=>{log('Connection error. Check network or API access.');setStatus('Connection error')};
  ws.onclose=()=>{connected=false;setStatus('Disconnected');$('connect').textContent='Connect live ticks';log('Connection closed')};
}
$('connect').onclick=()=>connected?disconnect():connect();
$('market').onchange=()=>{if(connected)connect()};
$('window').onchange=render;$('barrier').onchange=render;
document.querySelectorAll('.mode').forEach(b=>b.onclick=()=>{
  mode=b.dataset.mode;document.querySelectorAll('.mode').forEach(x=>x.classList.toggle('active',x===b));
  $('barrierWrap').style.display=['OVER','UNDER','MATCHES','DIFFERS'].includes(mode)?'block':'none';render();
});
render();
</script>
</body>
</html>
