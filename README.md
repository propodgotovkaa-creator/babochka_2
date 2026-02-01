<!doctype html>
<html lang="ru">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,height=device-height,initial-scale=1.0" />
<title>Genially: Редактор «Найди персонажа»</title>

<style>
  :root{
    --bg:#0b1020;
    --panel:rgba(20,24,40,.82);
    --line:rgba(255,255,255,.14);
    --txt:rgba(255,255,255,.92);
    --muted:rgba(255,255,255,.65);
    --btn:#2d6cdf;
    --btn2:#16a34a;
  }
  *{box-sizing:border-box}
  html,body{height:100%; margin:0; font-family:system-ui,-apple-system,Segoe UI,Roboto,Arial; background:var(--bg); color:var(--txt);}
  .wrap{display:grid; grid-template-columns: 360px 1fr; height:100%;}

  .panel{
    background:var(--panel);
    border-right:1px solid var(--line);
    padding:14px;
    overflow:auto;
    backdrop-filter: blur(8px);
  }
  .panel h1{font-size:16px;margin:0 0 12px}
  .row{margin:10px 0}
  label{display:block;font-size:12px;color:var(--muted);margin-bottom:6px}
  input[type="text"], input[type="number"], select, textarea{
    width:100%; padding:10px; border-radius:10px;
    border:1px solid var(--line); background:rgba(255,255,255,.06);
    color:var(--txt); outline:none;
  }
  input[type="range"]{width:100%}
  textarea{resize:vertical; min-height:80px}
  .two{display:grid; grid-template-columns: 1fr 1fr; gap:10px}
  .btns{display:flex; gap:10px; flex-wrap:wrap; margin-top:10px}
  button{
    border:0; border-radius:10px; padding:10px 12px; cursor:pointer;
    color:#fff; font-weight:700;
  }
  .b1{background:var(--btn)}
  .b2{background:var(--btn2)}
  .b3{background:rgba(255,255,255,.12); border:1px solid var(--line); color:var(--txt); font-weight:700}

  .stage{
    position:relative;
    background:
      linear-gradient(0deg, rgba(255,255,255,.04), rgba(255,255,255,.04)),
      repeating-linear-gradient(45deg, rgba(255,255,255,.035) 0 12px, rgba(255,255,255,.02) 12px 24px);
    overflow:hidden;
  }

  .progBadge{
    position:absolute; right:14px; top:14px; z-index:20;
    background:rgba(0,0,0,.45); border:1px solid rgba(255,255,255,.18);
    padding:6px 10px; border-radius:999px; font-size:12px; color:rgba(255,255,255,.9);
    backdrop-filter:blur(6px);
    pointer-events:none;
  }

  .hintBtn{
    position:absolute;
    right:64px; top:12px;
    z-index:26;
    width:38px; height:38px;
    border-radius:12px;
    border:1px solid rgba(255,255,255,.18);
    background:rgba(0,0,0,.35);
    color:rgba(255,255,255,.92);
    font-size:18px;
    cursor:pointer;
    display:flex; align-items:center; justify-content:center;
    backdrop-filter: blur(6px);
    box-shadow: 0 12px 30px rgba(0,0,0,.18);
    -webkit-tap-highlight-color: transparent;
  }
  .hintBtn:hover{ background:rgba(0,0,0,.45); }
  .hintBtn:active{ transform: translateY(1px); }
  .hintBtn:disabled{ opacity:.45; cursor:not-allowed; }

  .centerTask{
    position:absolute; left:50%; top:18px; transform:translateX(-50%);
    z-index:18;
    max-width:min(720px, calc(100% - 40px));
    background:rgba(0,0,0,.42);
    border:1px solid rgba(255,255,255,.18);
    border-radius:14px;
    padding:10px 14px;
    color:rgba(255,255,255,.95);
    font:700 14px/1.25 system-ui,-apple-system,Segoe UI,Roboto,Arial;
    text-align:center;
    backdrop-filter: blur(8px);
    pointer-events:none;
    box-shadow: 0 12px 30px rgba(0,0,0,.22);
  }

  .finalOverlay{
    position:absolute; inset:0; z-index:30;
    display:none;
    align-items:center;
    justify-content:center;
    padding:24px;
    background:rgba(0,0,0,.18);
    backdrop-filter: blur(2px);
  }
  .finalCard{
    max-width:min(760px, calc(100% - 40px));
    background:rgba(0,0,0,.55);
    border:1px solid rgba(255,255,255,.18);
    border-radius:18px;
    padding:18px 18px;
    color:rgba(255,255,255,.96);
    font:800 18px/1.25 system-ui,-apple-system,Segoe UI,Roboto,Arial;
    text-align:center;
    box-shadow: 0 18px 50px rgba(0,0,0,.28);
  }

  .target{
    position:absolute;
    width:46px; height:46px;
    cursor:pointer;
    user-select:none;
    z-index:25;
    -webkit-tap-highlight-color: transparent;
  }
  .target img{display:block;width:100%;height:100%;object-fit:contain}

  @keyframes fxFadeIn{from{opacity:0} to{opacity:1}}
  @keyframes fxPopIn{0%{opacity:0; transform:scale(.7)} 100%{opacity:1; transform:scale(1)}}
  @keyframes fxSlideIn{0%{opacity:0; transform:translateY(-10px)} 100%{opacity:1; transform:translateY(0)}}
  @keyframes fxWiggle{0%{opacity:0; transform:scale(.9) rotate(-4deg)} 70%{opacity:1; transform:scale(1.02) rotate(2deg)} 100%{opacity:1; transform:scale(1) rotate(0deg)}}
  @keyframes fxSoftFade{0%{opacity:0; transform:scale(.96)} 100%{opacity:1; transform:scale(1)}}

  .fx-none{}
  .fx-fade{animation: fxFadeIn var(--dur,500ms) ease-out}
  .fx-pop{animation: fxPopIn var(--dur,500ms) cubic-bezier(.2,.8,.2,1)}
  .fx-slide{animation: fxSlideIn var(--dur,500ms) ease-out}
  .fx-wiggle{animation: fxWiggle var(--dur,650ms) cubic-bezier(.2,.8,.2,1)}
  .fx-softfade{animation: fxSoftFade var(--dur,1100ms) ease-out}

  @keyframes fxHintPulse{
    0%   { transform:scale(1);   opacity:1; filter:drop-shadow(0 0 0 rgba(120,220,255,0)); }
    20%  { transform:scale(1.25);opacity:1; filter:drop-shadow(0 0 14px rgba(120,220,255,.95)); }
    40%  { transform:scale(1);   opacity:.35; filter:drop-shadow(0 0 0 rgba(120,220,255,0)); }
    60%  { transform:scale(1.25);opacity:1; filter:drop-shadow(0 0 16px rgba(120,220,255,.98)); }
    80%  { transform:scale(1);   opacity:.35; filter:drop-shadow(0 0 0 rgba(120,220,255,0)); }
    100% { transform:scale(1);   opacity:1; filter:drop-shadow(0 0 0 rgba(120,220,255,0)); }
  }
  .target.fx-hint img{ animation: fxHintPulse 900ms ease-in-out 0s 2; }

  .codeBox{height:260px; font-family:ui-monospace, SFMono-Regular, Menlo, Consolas, monospace; font-size:12px; line-height:1.35;}
</style>
</head>

<body>
<div class="wrap">
  <div class="panel">
    <h1>Редактор «Найди персонажа»</h1>

    <div class="row">
      <label>URL картинки персонажа</label>
      <input id="imgUrl" type="text" />
      <div id="imgStatus" style="font-size:12px;color:var(--muted);margin-top:6px"></div>
    </div>

    <div class="two">
      <div class="row">
        <label>Размер (px): <span id="sizeVal">46</span></label>
        <input id="size" type="range" min="18" max="220" value="46" />
      </div>
      <div class="row">
        <label>Количество находок (раундов)</label>
        <input id="rounds" type="number" min="1" max="99" value="7" />
      </div>
    </div>

    <div class="two">
      <div class="row">
        <label>Анимация появления</label>
        <select id="anim">
          <option value="none">без анимации</option>
          <option value="softfade" selected>soft fade (медленно)</option>
          <option value="fade">fade</option>
          <option value="pop">pop</option>
          <option value="slide">slide</option>
          <option value="wiggle">wiggle</option>
        </select>
      </div>
      <div class="row">
        <label>Длительность анимации (мс)</label>
        <input id="dur" type="number" min="0" max="3000" value="1100" />
      </div>
    </div>

    <div class="row">
      <label>Инструкция / текст задания (плашка сверху по центру)</label>
      <input id="taskText" type="text" value="Найди бабочку и нажми на неё!" />
    </div>

    <div class="row">
      <label>Текст в конце игры</label>
      <textarea id="endText">Молодец! Приходи играть ещё раз 😊</textarea>
    </div>

    <div class="two">
      <div class="row">
        <label>Отступ от краёв (px)</label>
        <input id="pad" type="number" min="0" max="240" value="12" />
      </div>
      <div class="row">
        <label>Пауза между раундами (мс)</label>
        <input id="gap" type="number" min="0" max="8000" value="200" />
      </div>
    </div>

    <div class="btns">
      <button class="b3" id="reset">Сброс</button>
      <button class="b1" id="getCode">Получить код</button>
      <button class="b2" id="copyCode">Копировать код</button>
    </div>

    <div class="row">
      <label>Код для вставки в Genially (HTML / Embed)</label>
      <textarea id="code" class="codeBox" spellcheck="false"></textarea>
    </div>
  </div>

  <div class="stage" id="stage">
    <div class="progBadge" id="progBadge">0/7</div>
    <button class="hintBtn" id="hintBtn" title="Подсказка" aria-label="Подсказка">💡</button>
    <div class="centerTask" id="centerTask">Найди бабочку и нажми на неё!</div>

    <div class="target fx-softfade" id="target" style="--dur:1100ms;">
      <img id="targetImg" alt="" />
    </div>

    <div class="finalOverlay" id="finalOverlay">
      <div class="finalCard" id="finalCard">Молодец! Приходи играть ещё раз 😊</div>
    </div>
  </div>
</div>

<script>
  const $ = (id)=>document.getElementById(id);

  // ✅ babochka_2
  const DEFAULT_URL = "https://propodgotovkaa-creator.github.io/babochka_2/babochka.png";

  const imgUrl = $("imgUrl");
  const imgStatus = $("imgStatus");
  const size = $("size");
  const sizeVal = $("sizeVal");
  const rounds = $("rounds");
  const anim = $("anim");
  const dur = $("dur");
  const pad = $("pad");
  const gap = $("gap");
  const taskText = $("taskText");
  const endText = $("endText");

  const stage = $("stage");
  const target = $("target");
  const targetImg = $("targetImg");
  const progBadge = $("progBadge");
  const centerTask = $("centerTask");
  const hintBtn = $("hintBtn");

  const finalOverlay = $("finalOverlay");
  const finalCard = $("finalCard");
  const codeBox = $("code");

  let found = 0;
  let last = {x:null, y:null};

  // память последних позиций (уменьшает повторы)
  const HISTORY_MAX = 10;
  const history = [];
  function pushHistory(pt){
    history.push({x:pt.x, y:pt.y});
    if(history.length > HISTORY_MAX) history.shift();
  }

  function clamp(n,a,b){ return Math.max(a, Math.min(b, n)); }
  function rect(){
    const r = stage.getBoundingClientRect();
    return {w: Math.max(1, r.width), h: Math.max(1, r.height)};
  }

  function setTargetSize(px){
    target.style.width = px + "px";
    target.style.height = px + "px";
    sizeVal.textContent = px;
  }

  function setAnim(){
    target.classList.remove("fx-none","fx-fade","fx-pop","fx-slide","fx-wiggle","fx-softfade");
    const v = anim.value;
    target.classList.add(v==="none" ? "fx-none" : ("fx-"+v));
    target.style.setProperty("--dur", Math.max(0, +dur.value || 0) + "ms");
  }

  function updateTexts(){
    centerTask.textContent = (taskText.value || "Найди персонажа!").trim();
    finalCard.textContent = (endText.value || "Молодец!").trim();
  }

  function updateProgress(){
    const total = Math.max(1, +rounds.value || 1);
    progBadge.textContent = `${found}/${total}`;
  }

  function intersects(a, b){
    return !(a.x + a.w <= b.x || b.x + b.w <= a.x || a.y + a.h <= b.y || b.y + b.h <= a.y);
  }

  function toStageRect(el){
    if(!el) return null;
    const sr = stage.getBoundingClientRect();
    const r = el.getBoundingClientRect();
    return { x: r.left - sr.left, y: r.top - sr.top, w: r.width, h: r.height };
  }

  // Запретные зоны: плашки + логотип Genially + зона стрелки справа
  function getForbiddenRects(){
    const rects = [];
    const a = toStageRect(centerTask);
    const b = toStageRect(progBadge);
    const c = toStageRect(hintBtn);

    const padUI = 10;
    for(const r of [a,b,c]){
      if(!r) continue;
      rects.push({ x: r.x - padUI, y: r.y - padUI, w: r.w + padUI*2, h: r.h + padUI*2 });
    }

    const {w,h} = rect();

    // Лого Genially (слева снизу)
    const logoW = 190;
    const logoH = 70;
    const logoPad = 8;
    rects.push({ x: 0, y: h - logoH - logoPad, w: logoW + logoPad, h: logoH + logoPad });

    // Зона стрелки "следующий слайд" (справа)
    const navW = 90;
    const navPad = 6;
    rects.push({ x: w - navW - navPad, y: 0, w: navW + navPad, h: h });

    return rects;
  }

  function distanceToHistory(pt){
    if(history.length === 0) return Infinity;
    let minD = Infinity;
    for(const p of history){
      const d = Math.hypot(pt.x - p.x, pt.y - p.y);
      if(d < minD) minD = d;
    }
    return minD;
  }

  function randomPos(){
    const {w,h} = rect();
    const s = parseFloat(target.style.width) || 46;
    const p = clamp(+pad.value || 0, 0, 240);

    const minX = p;
    const minY = p;
    const maxX = Math.max(minX, w - p - s);
    const maxY = Math.max(minY, h - p - s);

    const forbid = getForbiddenRects();
    const candidateRect = (pt)=>({x:pt.x,y:pt.y,w:s,h:s});
    const randPoint = ()=>({ x:minX+Math.random()*(maxX-minX), y:minY+Math.random()*(maxY-minY) });

    const N = 90;
    let best = null;
    let bestScore = -1;

    for(let i=0;i<N;i++){
      const pt = randPoint();
      const cr = candidateRect(pt);
      if(forbid.some(fr => intersects(cr, fr))) continue;

      const dHist = distanceToHistory(pt);
      const dLast = (last.x===null) ? dHist : Math.hypot(pt.x-last.x, pt.y-last.y);

      // 🔧 редактору оставим как есть (он и так норм),
      // а в виджете подкрутим (см buildWidgetCode ниже)
      const score = dHist*1.25 + dLast*0.45 + Math.random()*4;

      if(score > bestScore){ bestScore=score; best=pt; }
    }

    if(!best){
      // fallback: несколько попыток "хоть куда-то" без пересечения
      for(let k=0;k<40;k++){
        const pt = randPoint();
        const cr = candidateRect(pt);
        if(!forbid.some(fr => intersects(cr, fr))){
          best = pt;
          break;
        }
      }
      if(!best) best = {x:minX, y:minY};
    }

    last = best;
    pushHistory(best);
    return best;
  }

  function place(){
    const pos = randomPos();
    target.style.left = pos.x + "px";
    target.style.top  = pos.y + "px";
  }

  function showFinal(){
    target.style.display = "none";
    finalOverlay.style.display = "flex";
    hintBtn.disabled = true;
  }

  function hideFinal(){
    finalOverlay.style.display = "none";
    hintBtn.disabled = false;
  }

  function next(){
    const total = Math.max(1, +rounds.value || 1);
    if(found >= total){
      showFinal();
      return;
    }
    hideFinal();
    target.style.display = "";

    target.classList.remove("fx-none","fx-fade","fx-pop","fx-slide","fx-wiggle","fx-softfade","fx-hint");
    void target.offsetWidth;

    setAnim();
    place();
    updateProgress();
  }

  function resetGame(){
    found = 0;
    last = {x:null,y:null};
    history.length = 0;
    hideFinal();
    updateTexts();
    setTargetSize(+size.value || 46);
    setAnim();
    updateProgress();
    place();
    target.style.display = "";
  }

  function runHint(){
    if(finalOverlay.style.display === "flex") return;

    const wasHidden = (target.style.display === "none");
    if(wasHidden) target.style.display = "";

    target.classList.remove("fx-hint");
    void target.offsetWidth;
    target.classList.add("fx-hint");

    setTimeout(()=>{
      target.classList.remove("fx-hint");
      if(wasHidden) target.style.display = "none";
    }, 2000);
  }

  // --- Генерация кода для Genially (самая важная часть: фиксы под Genially) ---
  function buildWidgetCode(cfg){
    const configJson = JSON.stringify(cfg);

    return `<div id="FX_ROOT">
  <style>
    #FX_ROOT{position:relative;width:100%;height:100%;overflow:hidden;background:transparent;}

    #FX_ROOT .fx_prog{
      position:absolute; right:14px; top:14px; z-index:999999;
      background:rgba(0,0,0,.45); border:1px solid rgba(255,255,255,.18);
      padding:6px 10px; border-radius:999px; font-size:12px; color:rgba(255,255,255,.9);
      backdrop-filter: blur(6px);
      pointer-events:none;
    }

    #FX_ROOT .fx_hintBtn{
      position:absolute; right:64px; top:12px;
      z-index:1000000;
      width:38px; height:38px;
      border-radius:12px;
      border:1px solid rgba(255,255,255,.18);
      background:rgba(0,0,0,.35);
      color:rgba(255,255,255,.92);
      font-size:18px;
      cursor:pointer;
      display:flex; align-items:center; justify-content:center;
      backdrop-filter: blur(6px);
      box-shadow: 0 12px 30px rgba(0,0,0,.18);
      -webkit-tap-highlight-color: transparent;
    }
    #FX_ROOT .fx_hintBtn:hover{ background:rgba(0,0,0,.45); }
    #FX_ROOT .fx_hintBtn:active{ transform: translateY(1px); }
    #FX_ROOT .fx_hintBtn:disabled{ opacity:.45; cursor:not-allowed; }

    #FX_ROOT .fx_task{
      position:absolute; left:50%; top:18px; transform:translateX(-50%);
      z-index:999998;
      max-width:min(720px, calc(100% - 40px));
      background:rgba(0,0,0,.42);
      border:1px solid rgba(255,255,255,.18);
      border-radius:14px;
      padding:10px 14px;
      color:rgba(255,255,255,.95);
      font:700 14px/1.25 system-ui,-apple-system,Segoe UI,Roboto,Arial;
      text-align:center;
      backdrop-filter: blur(8px);
      pointer-events:none;
      box-shadow: 0 12px 30px rgba(0,0,0,.22);
    }

    #FX_ROOT .fx_target{
      position:absolute; z-index:999999;
      cursor:pointer; user-select:none;
      -webkit-tap-highlight-color: transparent;
    }
    #FX_ROOT .fx_target img{display:block;width:100%;height:100%;object-fit:contain}

    #FX_ROOT .fx_final{
      position:absolute; inset:0; z-index:1000001;
      display:none; align-items:center; justify-content:center;
      padding:24px;
      background:rgba(0,0,0,.18);
      backdrop-filter: blur(2px);
      pointer-events:none;
    }
    #FX_ROOT .fx_finalCard{
      max-width:min(760px, calc(100% - 40px));
      background:rgba(0,0,0,.55);
      border:1px solid rgba(255,255,255,.18);
      border-radius:18px;
      padding:18px 18px;
      color:rgba(255,255,255,.96);
      font:800 18px/1.25 system-ui,-apple-system,Segoe UI,Roboto,Arial;
      text-align:center;
      box-shadow: 0 18px 50px rgba(0,0,0,.28);
    }

    @keyframes fxFadeIn{from{opacity:0} to{opacity:1}}
    @keyframes fxPopIn{0%{opacity:0; transform:scale(.7)} 100%{opacity:1; transform:scale(1)}}
    @keyframes fxSlideIn{0%{opacity:0; transform:translateY(-10px)} 100%{opacity:1; transform:translateY(0)}}
    @keyframes fxWiggle{0%{opacity:0; transform:scale(.9) rotate(-4deg)} 70%{opacity:1; transform:scale(1.02) rotate(2deg)} 100%{opacity:1; transform:scale(1) rotate(0deg)}}
    @keyframes fxSoftFade{0%{opacity:0; transform:scale(.96)} 100%{opacity:1; transform:scale(1)}}

    #FX_ROOT .fx-none{}
    #FX_ROOT .fx-fade{animation: fxFadeIn var(--dur,500ms) ease-out}
    #FX_ROOT .fx-pop{animation: fxPopIn var(--dur,500ms) cubic-bezier(.2,.8,.2,1)}
    #FX_ROOT .fx-slide{animation: fxSlideIn var(--dur,500ms) ease-out}
    #FX_ROOT .fx-wiggle{animation: fxWiggle var(--dur,650ms) cubic-bezier(.2,.8,.2,1)}
    #FX_ROOT .fx-softfade{animation: fxSoftFade var(--dur,1100ms) ease-out}

    @keyframes fxHintPulse{
      0%   { transform:scale(1);   opacity:1; filter:drop-shadow(0 0 0 rgba(120,220,255,0)); }
      20%  { transform:scale(1.25);opacity:1; filter:drop-shadow(0 0 14px rgba(120,220,255,.95)); }
      40%  { transform:scale(1);   opacity:.35; filter:drop-shadow(0 0 0 rgba(120,220,255,0)); }
      60%  { transform:scale(1.25);opacity:1; filter:drop-shadow(0 0 16px rgba(120,220,255,.98)); }
      80%  { transform:scale(1);   opacity:.35; filter:drop-shadow(0 0 0 rgba(120,220,255,0)); }
      100% { transform:scale(1);   opacity:1; filter:drop-shadow(0 0 0 rgba(120,220,255,0)); }
    }
    #FX_ROOT .fx_target.fx-hint img{ animation: fxHintPulse 900ms ease-in-out 0s 2; }
  </style>

  <div class="fx_prog"></div>
  <button class="fx_hintBtn" type="button" aria-label="Подсказка" title="Подсказка">💡</button>
  <div class="fx_task"></div>
  <div class="fx_target"><img alt=""></div>

  <div class="fx_final">
    <div class="fx_finalCard"></div>
  </div>

  <script>
  (function(){
    const root = document.getElementById('FX_ROOT');
    if(!root) return;

    const cfg = ${configJson};

    const progEl = root.querySelector('.fx_prog');
    const hintBtn = root.querySelector('.fx_hintBtn');
    const taskEl = root.querySelector('.fx_task');
    const target = root.querySelector('.fx_target');
    const img = target.querySelector('img');
    const final = root.querySelector('.fx_final');
    const finalCard = root.querySelector('.fx_finalCard');

    let found = 0;
    let last = {x:null, y:null};

    const HISTORY_MAX = 10;
    const history = [];
    function pushHistory(pt){
      history.push({x:pt.x, y:pt.y});
      if(history.length > HISTORY_MAX) history.shift();
    }

    function clamp(n,a,b){ return Math.max(a, Math.min(b, n)); }

    function rect(){
      const r = root.getBoundingClientRect();
      return {w: Math.max(1, r.width), h: Math.max(1, r.height)};
    }

    function intersects(a, b){
      return !(a.x + a.w <= b.x || b.x + b.w <= a.x || a.y + a.h <= b.y || b.y + b.h <= a.y);
    }

    function toRootRect(el){
      if(!el) return null;
      const sr = root.getBoundingClientRect();
      const r = el.getBoundingClientRect();
      return { x: r.left - sr.left, y: r.top - sr.top, w: r.width, h: r.height };
    }

    // ✅ FIX 1: дождаться стабильного размера контейнера (Genially часто сначала даёт кривой размер)
    function waitForStableSize(cb){
      let lastKey = null;
      let stable = 0;
      const tick = ()=>{
        const r = root.getBoundingClientRect();
        const key = Math.round(r.width) + 'x' + Math.round(r.height);
        if(key === lastKey){
          stable++;
          if(stable >= 3){
            cb();
            return;
          }
        } else {
          lastKey = key;
          stable = 0;
        }
        requestAnimationFrame(tick);
      };
      tick();
    }

    function getForbiddenRects(){
      const rects = [];
      const a = toRootRect(taskEl);
      const b = toRootRect(progEl);
      const c = toRootRect(hintBtn);

      const padUI = 10;
      for(const r of [a,b,c]){
        if(!r) continue;
        rects.push({ x: r.x - padUI, y: r.y - padUI, w: r.w + padUI*2, h: r.h + padUI*2 });
      }

      const {w,h} = rect();

      // лого Genially (слева снизу)
      const logoW = 190, logoH = 70, logoPad = 8;
      rects.push({ x: 0, y: h - logoH - logoPad, w: logoW + logoPad, h: logoH + logoPad });

      // зона стрелки "следующий слайд" (справа)
      const navW = 90, navPad = 6;
      rects.push({ x: w - navW - navPad, y: 0, w: navW + navPad, h: h });

      return rects;
    }

    function setAnim(){
      target.classList.remove('fx-none','fx-fade','fx-pop','fx-slide','fx-wiggle','fx-softfade','fx-hint');
      const v = cfg.anim || 'none';
      target.classList.add(v==='none' ? 'fx-none' : ('fx-'+v));
      target.style.setProperty('--dur', Math.max(0, cfg.dur|0) + 'ms');
    }

    function updateUI(){
      const total = Math.max(1, cfg.rounds|0);
      progEl.textContent = found + '/' + total;
      taskEl.textContent = (cfg.task || 'Найди персонажа!').trim();
      finalCard.textContent = (cfg.endText || 'Молодец!').trim();
    }

    function distanceToHistory(pt){
      if(history.length === 0) return Infinity;
      let minD = Infinity;
      for(const p of history){
        const d = Math.hypot(pt.x - p.x, pt.y - p.y);
        if(d < minD) minD = d;
      }
      return minD;
    }

    function randomPos(){
      const {w,h} = rect();
      const s = Math.max(1, cfg.size|0);
      const p = clamp(cfg.pad|0, 0, 240);

      const minX = p;
      const minY = p;
      const maxX = Math.max(minX, w - p - s);
      const maxY = Math.max(minY, h - p - s);

      const forbid = getForbiddenRects();
      const candidateRect = (pt)=>({x:pt.x,y:pt.y,w:s,h:s});
      const randPoint = ()=>({ x:minX+Math.random()*(maxX-minX), y:minY+Math.random()*(maxY-minY) });

      const N = 120;
      let best = null;
      let bestScore = -1;

      for(let i=0;i<N;i++){
        const pt = randPoint();
        const cr = candidateRect(pt);
        if(forbid.some(fr => intersects(cr, fr))) continue;

        const dHist = distanceToHistory(pt);
        const dLast = (last.x===null) ? dHist : Math.hypot(pt.x-last.x, pt.y-last.y);

        // ✅ FIX 2: меньше "прилипаний", больше разброса по областям (включая низ)
        const score = dHist*0.9 + dLast*0.35 + Math.random()*12;

        if(score > bestScore){ bestScore=score; best=pt; }
      }

      if(!best){
        // fallback: попробуем просто найти любую допустимую точку
        for(let k=0;k<80;k++){
          const pt = randPoint();
          const cr = candidateRect(pt);
          if(!forbid.some(fr => intersects(cr, fr))){
            best = pt;
            break;
          }
        }
        if(!best) best = {x:minX, y:minY};
      }

      last = best;
      pushHistory(best);
      return best;
    }

    function place(){
      const pos = randomPos();
      target.style.left = pos.x + 'px';
      target.style.top  = pos.y + 'px';
    }

    function showFinal(){
      target.style.display = 'none';
      final.style.display = 'flex';
      hintBtn.disabled = true;
    }
    function hideFinal(){
      final.style.display = 'none';
      hintBtn.disabled = false;
    }

    function next(){
      const total = Math.max(1, cfg.rounds|0);
      if(found >= total){ showFinal(); return; }
      hideFinal();
      target.style.display = '';

      target.classList.remove('fx-none','fx-fade','fx-pop','fx-slide','fx-wiggle','fx-softfade','fx-hint');
      void target.offsetWidth;

      setAnim();
      place();
      updateUI();
    }

    function runHint(){
      if(final.style.display === 'flex') return;

      const wasHidden = (target.style.display === 'none');
      if(wasHidden) target.style.display = '';

      target.classList.remove('fx-hint');
      void target.offsetWidth;
      target.classList.add('fx-hint');

      setTimeout(()=>{
        target.classList.remove('fx-hint');
        if(wasHidden) target.style.display = 'none';
      }, 2000);
    }

    img.referrerPolicy = 'no-referrer';
    img.src = cfg.url || '';
    target.style.width  = (cfg.size|0) + 'px';
    target.style.height = (cfg.size|0) + 'px';

    hintBtn.addEventListener('click', runHint);

    target.addEventListener('click', ()=>{
      const total = Math.max(1, cfg.rounds|0);
      if(found >= total) return;
      found++;
      updateUI();
      target.style.display = 'none';
      setTimeout(next, Math.max(0, cfg.gap|0));
    });

    // Resize safety
    const ro = ('ResizeObserver' in window) ? new ResizeObserver(()=>{ if(target.style.display!=='none') place(); }) : null;
    if(ro) ro.observe(root);
    window.addEventListener('resize', ()=>{ if(target.style.display!=='none') place(); }, {passive:true});

    // ✅ FIX 3: стартуем ТОЛЬКО когда размер стабилен
    waitForStableSize(()=>{
      updateUI();
      next();

      // ✅ страховка: Genially иногда дорисовывает UI чуть позже
      setTimeout(()=>{ if(target.style.display!=='none') place(); }, 300);
    });
  })();
  <\/script>
</div>`;
  }

  function currentCfg(){
    return {
      url: (imgUrl.value || "").trim(),
      size: Math.max(18, Math.min(320, +size.value || 46)),
      rounds: Math.max(1, Math.min(99, +rounds.value || 7)),
      anim: anim.value,
      dur: Math.max(0, Math.min(3000, +dur.value || 0)),
      pad: Math.max(0, Math.min(240, +pad.value || 0)),
      gap: Math.max(0, Math.min(8000, +gap.value || 0)),
      task: (taskText.value || "").trim(),
      endText: (endText.value || "").trim()
    };
  }

  // ---------- EVENTS ----------
  $("reset").addEventListener("click", resetGame);

  $("getCode").addEventListener("click", ()=>{
    const cfg = currentCfg();
    codeBox.value = buildWidgetCode(cfg);
    codeBox.focus();
    codeBox.select();
  });

  $("copyCode").addEventListener("click", async ()=>{
    if(!codeBox.value.trim()){
      const cfg = currentCfg();
      codeBox.value = buildWidgetCode(cfg);
    }
    try{
      await navigator.clipboard.writeText(codeBox.value);
      imgStatus.textContent = "✓ код скопирован";
    }catch(e){
      codeBox.focus(); codeBox.select();
      imgStatus.textContent = "⚠️ не смог скопировать автоматически — выделила код, нажми Cmd+C";
    }
  });

  hintBtn.addEventListener("click", runHint);

  target.addEventListener("click", ()=>{
    const total = Math.max(1, +rounds.value || 1);
    if(found >= total) return;
    found++;
    updateProgress();
    target.style.display = "none";
    setTimeout(next, Math.max(0, +gap.value || 0));
  });

  // ✅ LIVE размер в редакторе
  size.addEventListener("input", ()=>{
    setTargetSize(+size.value || 46);
    place();
  });
  size.addEventListener("change", ()=>{
    setTargetSize(+size.value || 46);
    place();
  });

  // Поддержка обновления текста
  rounds.addEventListener("change", ()=>{ updateProgress(); });
  taskText.addEventListener("input", ()=>{ updateTexts(); });
  endText.addEventListener("input", ()=>{ updateTexts(); });

  // INIT
  imgUrl.value = DEFAULT_URL;
  targetImg.referrerPolicy = "no-referrer";
  targetImg.src = DEFAULT_URL;

  updateTexts();
  resetGame();
</script>
</body>
</html>
