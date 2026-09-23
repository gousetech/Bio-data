<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <meta name="theme-color" content="#635bff" />
  <title>Luma Calculator</title>
  <style>
    @import url('https://fonts.googleapis.com/css2?family=Manrope:wght@400;500;600;700;800&display=swap');

    :root {
      --bg-a: #0d1025;
      --bg-b: #331d68;
      --panel: rgba(18, 20, 43, 0.72);
      --panel-border: rgba(255, 255, 255, 0.14);
      --text: #f7f7ff;
      --muted: #aaa9c5;
      --button: rgba(255, 255, 255, 0.08);
      --button-hover: rgba(255, 255, 255, 0.15);
      --accent: #8b7cff;
      --accent-2: #ec5cff;
      --danger: #ff7089;
      --shadow: 0 35px 80px rgba(0, 0, 0, 0.38);
    }

    body.light {
      --bg-a: #edeaff;
      --bg-b: #c9e9ff;
      --panel: rgba(255, 255, 255, 0.68);
      --panel-border: rgba(255, 255, 255, 0.8);
      --text: #22203a;
      --muted: #74728a;
      --button: rgba(70, 62, 118, 0.08);
      --button-hover: rgba(70, 62, 118, 0.15);
      --shadow: 0 35px 80px rgba(76, 62, 140, 0.2);
    }

    * { box-sizing: border-box; }

    body {
      margin: 0;
      min-height: 100vh;
      display: grid;
      place-items: center;
      overflow-x: hidden;
      color: var(--text);
      font-family: "Manrope", sans-serif;
      background: linear-gradient(140deg, var(--bg-a), var(--bg-b));
      transition: background .5s ease, color .35s ease;
    }

    .aurora {
      position: fixed;
      inset: 0;
      overflow: hidden;
      pointer-events: none;
    }

    .orb {
      position: absolute;
      border-radius: 50%;
      filter: blur(5px);
      opacity: .45;
      animation: drift 12s ease-in-out infinite alternate;
    }

    .orb.one { width: 320px; height: 320px; left: -80px; top: -70px; background: #5977ff; }
    .orb.two { width: 260px; height: 260px; right: -40px; bottom: -70px; background: #ff5cba; animation-delay: -4s; }
    .orb.three { width: 170px; height: 170px; right: 20%; top: 5%; background: #53f0df; animation-delay: -8s; }

    @keyframes drift {
      to { transform: translate(40px, 35px) scale(1.12); }
    }

    .app {
      width: min(94vw, 410px);
      margin: 28px 0;
      padding: 20px;
      position: relative;
      z-index: 1;
      border: 1px solid var(--panel-border);
      border-radius: 30px;
      background: var(--panel);
      box-shadow: var(--shadow);
      backdrop-filter: blur(24px);
      -webkit-backdrop-filter: blur(24px);
      animation: arrive .7s cubic-bezier(.2,.8,.2,1) both;
      transition: background .35s, border .35s;
    }

    @keyframes arrive {
      from { opacity: 0; transform: translateY(28px) scale(.96); }
    }

    .topbar { display: flex; align-items: center; justify-content: space-between; margin-bottom: 18px; }
    .brand { display: flex; align-items: center; gap: 10px; font-weight: 800; letter-spacing: -.4px; }
    .logo {
      width: 35px; height: 35px; display: grid; place-items: center; border-radius: 12px;
      background: linear-gradient(135deg, var(--accent), var(--accent-2));
      box-shadow: 0 8px 24px rgba(139,124,255,.35);
    }
    .controls { display: flex; gap: 8px; }
    .select, .icon-btn {
      color: var(--text); border: 1px solid var(--panel-border); background: var(--button);
      border-radius: 12px; height: 36px; outline: none; cursor: pointer;
    }
    .select { padding: 0 8px; font: 600 12px inherit; }
    .select option { color: #222; }
    .icon-btn { width: 36px; font-size: 16px; transition: transform .2s, background .2s; }
    .icon-btn:hover { transform: rotate(12deg); background: var(--button-hover); }

    .screen {
      min-height: 142px;
      padding: 18px 8px 20px;
      display: flex;
      flex-direction: column;
      align-items: flex-end;
      justify-content: flex-end;
      overflow: hidden;
    }
    .status { width: 100%; display: flex; justify-content: space-between; color: var(--muted); font-size: 11px; font-weight: 700; }
    .expression {
      width: 100%; min-height: 27px; margin-top: 12px; text-align: right;
      color: var(--muted); font-size: 18px; white-space: nowrap; overflow: hidden; text-overflow: ellipsis;
    }
    .result {
      width: 100%; text-align: right; font-size: clamp(36px, 10vw, 52px);
      font-weight: 800; letter-spacing: -2.5px; white-space: nowrap; overflow: hidden; text-overflow: ellipsis;
      transition: transform .18s, opacity .18s;
    }
    .result.pop { animation: resultPop .28s ease; }
    @keyframes resultPop { 50% { transform: scale(1.045); color: var(--accent); } }

    .memory-row { display: grid; grid-template-columns: repeat(5, 1fr); gap: 7px; margin-bottom: 9px; }
    .memory-row button { height: 34px; border-radius: 11px; color: var(--muted); font-size: 11px; font-weight: 800; }
    .keypad { display: grid; grid-template-columns: repeat(4, 1fr); gap: 10px; }
    button.key, .memory-row button {
      border: 0; background: var(--button); cursor: pointer; font-family: inherit;
      transition: transform .1s ease, background .2s ease, box-shadow .2s ease;
      -webkit-tap-highlight-color: transparent;
    }
    button.key {
      aspect-ratio: 1.15; border-radius: 18px; color: var(--text); font-size: 20px; font-weight: 700;
    }
    button.key:hover, .memory-row button:hover { background: var(--button-hover); transform: translateY(-2px); }
    button.key:active, .memory-row button:active { transform: scale(.91); }
    .key.utility { color: var(--danger); }
    .key.operator { color: #c9c2ff; background: rgba(139,124,255,.15); }
    .key.equals {
      color: white; background: linear-gradient(135deg, var(--accent), var(--accent-2));
      box-shadow: 0 10px 25px rgba(139,124,255,.32);
    }
    .key.equals:hover { background: linear-gradient(135deg, #9f92ff, #f477ff); }

    .history-panel {
      max-height: 0; overflow: hidden; opacity: 0; transition: max-height .45s ease, opacity .3s, margin .3s;
    }
    .history-panel.open { max-height: 180px; opacity: 1; margin-bottom: 12px; }
    .history-title { display: flex; justify-content: space-between; align-items: center; font-size: 12px; color: var(--muted); font-weight: 800; }
    .clear-history { color: var(--danger); border: 0; background: none; cursor: pointer; font: 700 11px inherit; }
    .history-list { max-height: 145px; overflow-y: auto; padding-top: 8px; }
    .history-item { padding: 8px; border-radius: 10px; text-align: right; cursor: pointer; transition: background .2s; }
    .history-item:hover { background: var(--button); }
    .history-item small { display: block; color: var(--muted); }
    .history-item strong { font-size: 15px; }
    .empty { text-align: center; color: var(--muted); padding: 22px 0; font-size: 12px; }

    .shake { animation: shake .35s ease; }
    @keyframes shake { 25% { transform: translateX(-7px); } 50% { transform: translateX(7px); } 75% { transform: translateX(-4px); } }

    @media (max-width: 380px) {
      .app { padding: 15px; border-radius: 24px; }
      .keypad { gap: 7px; }
      button.key { border-radius: 15px; }
      .brand span:last-child { display: none; }
    }

    @media (prefers-reduced-motion: reduce) {
      *, *::before, *::after { animation-duration: .01ms !important; animation-iteration-count: 1 !important; transition-duration: .01ms !important; }
    }
  </style>
</head>
<body>
  <div class="aurora" aria-hidden="true">
    <span class="orb one"></span><span class="orb two"></span><span class="orb three"></span>
  </div>

  <main class="app" id="app">
    <header class="topbar">
      <div class="brand"><span class="logo">✦</span><span>Luma</span></div>
      <div class="controls">
        <select class="select" id="language" aria-label="Choose language">
          <option value="en">EN</option><option value="sw">SW</option><option value="fr">FR</option>
        </select>
        <button class="icon-btn" id="historyButton" title="History" aria-label="Open history">↻</button>
        <button class="icon-btn" id="themeButton" title="Change theme" aria-label="Change theme">☀</button>
      </div>
    </header>

    <section class="screen" aria-live="polite">
      <div class="status"><span id="memoryStatus"></span><span id="modeLabel">STANDARD</span></div>
      <div class="expression" id="expression">&nbsp;</div>
      <div class="result" id="result">0</div>
    </section>

    <section class="history-panel" id="historyPanel">
      <div class="history-title"><span id="historyTitle">HISTORY</span><button class="clear-history" id="clearHistory">CLEAR</button></div>
      <div class="history-list" id="historyList"></div>
    </section>

    <div class="memory-row">
      <button data-action="mc">MC</button><button data-action="mr">MR</button><button data-action="mplus">M+</button><button data-action="mminus">M−</button><button data-action="ms">MS</button>
    </div>

    <section class="keypad" aria-label="Calculator keypad">
      <button class="key utility" data-action="clear">AC</button>
      <button class="key utility" data-action="backspace">⌫</button>
      <button class="key operator" data-value="%">%</button>
      <button class="key operator" data-value="÷">÷</button>
      <button class="key" data-value="7">7</button><button class="key" data-value="8">8</button><button class="key" data-value="9">9</button><button class="key operator" data-value="×">×</button>
      <button class="key" data-value="4">4</button><button class="key" data-value="5">5</button><button class="key" data-value="6">6</button><button class="key operator" data-value="−">−</button>
      <button class="key" data-value="1">1</button><button class="key" data-value="2">2</button><button class="key" data-value="3">3</button><button class="key operator" data-value="+">+</button>
      <button class="key" data-action="sign">±</button><button class="key" data-value="0">0</button><button class="key" data-value=".">.</button><button class="key equals" data-action="equals">=</button>
    </section>
  </main>

  <script>
    const $ = (selector) => document.querySelector(selector);
    const expressionEl = $('#expression');
    const resultEl = $('#result');
    const app = $('#app');
    let expression = '';
    let displayedValue = '0';
    let justCalculated = false;
    let memory = Number(localStorage.getItem('luma-memory') || 0);
    let history = JSON.parse(localStorage.getItem('luma-history') || '[]');

    const translations = {
      en: { standard: 'STANDARD', history: 'HISTORY', clear: 'CLEAR', empty: 'No calculations yet', error: 'Error', language: 'Choose language', historyLabel: 'Open history', theme: 'Change theme' },
      sw: { standard: 'KAWAIDA', history: 'HISTORIA', clear: 'FUTA', empty: 'Hakuna hesabu bado', error: 'Hitilafu', language: 'Chagua lugha', historyLabel: 'Fungua historia', theme: 'Badili mandhari' },
      fr: { standard: 'STANDARD', history: 'HISTORIQUE', clear: 'EFFACER', empty: 'Aucun calcul', error: 'Erreur', language: 'Choisir la langue', historyLabel: "Ouvrir l'historique", theme: 'Changer le thème' }
    };

    let lang = localStorage.getItem('luma-language') || 'en';
    $('#language').value = lang;

    function translate() {
      const t = translations[lang];
      $('#modeLabel').textContent = t.standard;
      $('#historyTitle').textContent = t.history;
      $('#clearHistory').textContent = t.clear;
      $('#language').setAttribute('aria-label', t.language);
      $('#historyButton').setAttribute('aria-label', t.historyLabel);
      $('#themeButton').setAttribute('aria-label', t.theme);
      renderHistory();
    }

    function formatNumber(value) {
      if (!Number.isFinite(value)) return translations[lang].error;
      const rounded = Math.abs(value) < 1e-12 ? 0 : Number(value.toPrecision(12));
      return new Intl.NumberFormat(lang === 'sw' ? 'sw-TZ' : lang, { maximumFractionDigits: 10 }).format(rounded);
    }

    function normalize(input) {
      let safe = input.replace(/×/g, '*').replace(/÷/g, '/').replace(/−/g, '-');
      safe = safe.replace(/(\d+(?:\.\d+)?)%/g, '($1/100)');
      if (!/^[\d+\-*/().\s]+$/.test(safe)) throw new Error('Unsafe expression');
      return safe;
    }

    function evaluate(input = expression) {
      if (!input) return 0;
      const safe = normalize(input);
      const value = Function('"use strict"; return (' + safe + ')')();
      if (typeof value !== 'number' || !Number.isFinite(value)) throw new Error('Invalid result');
      return value;
    }

    function updateDisplay(preview = true) {
      expressionEl.textContent = expression || '\u00a0';
      if (preview && expression && !/[+−×÷.]$/.test(expression)) {
        try { displayedValue = formatNumber(evaluate()); } catch (_) {}
      }
      resultEl.textContent = displayedValue;
      $('#memoryStatus').textContent = memory !== 0 ? 'M' : '';
    }

    function animateResult() {
      resultEl.classList.remove('pop');
      void resultEl.offsetWidth;
      resultEl.classList.add('pop');
    }

    function showError() {
      displayedValue = translations[lang].error;
      updateDisplay(false);
      app.classList.remove('shake'); void app.offsetWidth; app.classList.add('shake');
    }

    function inputValue(value) {
      const operators = '+−×÷';
      if (justCalculated && !operators.includes(value)) expression = '';
      justCalculated = false;
      if (operators.includes(value)) {
        if (!expression && value !== '−') return;
        if (operators.includes(expression.slice(-1))) expression = expression.slice(0, -1) + value;
        else expression += value;
      } else if (value === '.') {
        const current = expression.split(/[+−×÷]/).pop();
        if (current.includes('.')) return;
        expression += current ? '.' : '0.';
      } else {
        if (expression === '0') expression = value;
        else expression += value;
      }
      updateDisplay();
    }

    function calculate() {
      if (!expression) return;
      try {
        const original = expression;
        const value = evaluate();
        displayedValue = formatNumber(value);
        expression = String(Number(value.toPrecision(12))).replace('-', '−');
        history.unshift({ expression: original, result: displayedValue });
        history = history.slice(0, 12);
        localStorage.setItem('luma-history', JSON.stringify(history));
        justCalculated = true;
        updateDisplay(false); renderHistory(); animateResult();
      } catch (_) { showError(); }
    }

    function action(name) {
      if (name === 'clear') { expression = ''; displayedValue = '0'; justCalculated = false; updateDisplay(false); }
      if (name === 'backspace') { expression = expression.slice(0, -1); displayedValue = expression ? displayedValue : '0'; updateDisplay(); }
      if (name === 'equals') calculate();
      if (name === 'sign') {
        if (!expression) expression = '−';
        else if (/^−[\d.]+$/.test(expression)) expression = expression.slice(1);
        else if (/^[\d.]+$/.test(expression)) expression = '−' + expression;
        updateDisplay();
      }
      if (name === 'mc') { memory = 0; saveMemory(); }
      if (name === 'mr') { expression += String(memory).replace('-', '−'); updateDisplay(); }
      if (name === 'ms') { try { memory = evaluate(); saveMemory(); } catch (_) { showError(); } }
      if (name === 'mplus') { try { memory += evaluate(); saveMemory(); } catch (_) { showError(); } }
      if (name === 'mminus') { try { memory -= evaluate(); saveMemory(); } catch (_) { showError(); } }
    }

    function saveMemory() { localStorage.setItem('luma-memory', memory); updateDisplay(false); animateResult(); }

    function renderHistory() {
      const list = $('#historyList');
      list.innerHTML = '';
      if (!history.length) { list.innerHTML = `<div class="empty">${translations[lang].empty}</div>`; return; }
      history.forEach((item) => {
        const row = document.createElement('div'); row.className = 'history-item';
        row.innerHTML = `<small></small><strong></strong>`;
        row.querySelector('small').textContent = item.expression;
        row.querySelector('strong').textContent = '= ' + item.result;
        row.addEventListener('click', () => { expression = item.expression; updateDisplay(); });
        list.appendChild(row);
      });
    }

    document.addEventListener('click', (event) => {
      const button = event.target.closest('button');
      if (!button) return;
      if (button.dataset.value) inputValue(button.dataset.value);
      if (button.dataset.action) action(button.dataset.action);
    });

    document.addEventListener('keydown', (event) => {
      const map = { '*': '×', '/': '÷', '-': '−', Enter: '=', '=': '=', Escape: 'AC', Backspace: '⌫' };
      const key = map[event.key] || event.key;
      if (/^[0-9.+%]$/.test(key) || ['×','÷','−'].includes(key)) inputValue(key);
      if (key === '=') calculate();
      if (key === 'AC') action('clear');
      if (key === '⌫') action('backspace');
    });

    $('#historyButton').addEventListener('click', () => $('#historyPanel').classList.toggle('open'));
    $('#clearHistory').addEventListener('click', () => { history = []; localStorage.removeItem('luma-history'); renderHistory(); });
    $('#language').addEventListener('change', (event) => { lang = event.target.value; localStorage.setItem('luma-language', lang); translate(); updateDisplay(); });
    $('#themeButton').addEventListener('click', () => {
      document.body.classList.toggle('light');
      const light = document.body.classList.contains('light');
      $('#themeButton').textContent = light ? '☾' : '☀';
      localStorage.setItem('luma-theme', light ? 'light' : 'dark');
    });

    if (localStorage.getItem('luma-theme') === 'light') { document.body.classList.add('light'); $('#themeButton').textContent = '☾'; }
    translate(); updateDisplay(false);
  </script>
</body>
</html>
