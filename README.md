[friends-talk-wheel-prizes-fixed.html](https://github.com/user-attachments/files/27053730/friends-talk-wheel-prizes-fixed.html)
<!DOCTYPE html>
<html lang="ru">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Friends Talk — Колесо Фортуны</title>
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Cormorant+Garamond:wght@500;600;700&family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
  <style>
    :root { --bg:#14110f; --surface:#1d1815; --border:rgba(222,199,169,.16); --text:#f4eadf; --muted:#cdb9a3; --faint:#9d8978; --gold:#d7b07a; --shadow:0 20px 60px rgba(0,0,0,.45); --font-display:'Cormorant Garamond',serif; --font-body:'Inter',sans-serif; }
    *{box-sizing:border-box;margin:0;padding:0}
    body{min-height:100dvh;display:grid;place-items:center;padding:16px;background:radial-gradient(circle at 20% 20%, rgba(197,139,88,.12), transparent 35%),radial-gradient(circle at 80% 10%, rgba(255,240,220,.06), transparent 26%),#14110f;color:var(--text);font-family:var(--font-body)}
    .panel{width:min(100%,980px);background:linear-gradient(180deg,#1d1815 0%,#171210 100%);border:1px solid var(--border);border-radius:28px;box-shadow:var(--shadow);padding:clamp(16px,3vw,32px)}
    .wheel-wrap{position:relative;width:min(88vw,560px);aspect-ratio:1/1;margin:0 auto 24px}
    .pointer{position:absolute;left:50%;transform:translateX(-50%);top:-8px;z-index:10;width:0;height:0;border-left:18px solid transparent;border-right:18px solid transparent;border-top:34px solid #f4eadf;filter:drop-shadow(0 8px 12px rgba(0,0,0,.45))}
    canvas{width:100%;height:100%;border-radius:50%;display:block;filter:drop-shadow(0 30px 50px rgba(0,0,0,.42))}
    .hub{position:absolute;inset:50% auto auto 50%;transform:translate(-50%,-50%);width:126px;height:126px;border-radius:50%;background:radial-gradient(circle at 35% 35%, #f1d7b5 0%, #c69460 38%, #7a4d28 100%);border:10px solid rgba(255,245,232,.12);box-shadow:0 0 0 10px rgba(197,139,88,.08), inset 0 12px 26px rgba(255,255,255,.18), 0 16px 28px rgba(0,0,0,.34);display:grid;place-items:center;text-align:center;padding:12px}
    .hub span{font-family:var(--font-display);font-size:28px;line-height:1;color:#1c140f;text-transform:uppercase}
    .spin-wrap{display:flex;justify-content:center;margin:0 0 24px}
    .spin{min-height:56px;border:none;border-radius:999px;padding:0 28px;background:linear-gradient(180deg,#d3a16d 0%,#b97f4d 100%);color:#171210;font-size:14px;letter-spacing:.14em;text-transform:uppercase;font-weight:800;box-shadow:0 16px 30px rgba(185,127,77,.26), inset 0 1px 0 rgba(255,255,255,.25);cursor:pointer;transition:transform .18s ease,box-shadow .18s ease,opacity .18s ease}
    .spin:hover{transform:translateY(-1px);box-shadow:0 22px 38px rgba(185,127,77,.33), inset 0 1px 0 rgba(255,255,255,.25)}
    .spin:disabled{opacity:.45;cursor:not-allowed;transform:none}
    .prize-list{width:min(100%,760px);margin:0 auto;border:1px solid var(--border);border-radius:20px;background:rgba(255,255,255,.02);padding:16px 16px 12px}
    .prize-list h2{font-size:12px;letter-spacing:.18em;text-transform:uppercase;color:var(--faint);margin-bottom:12px;text-align:center}
    .chips{display:flex;flex-wrap:wrap;gap:8px;justify-content:center}
    .chip{padding:.78rem 1rem;border-radius:999px;border:1px solid rgba(215,176,122,.18);background:linear-gradient(180deg, rgba(255,255,255,.04), rgba(255,255,255,.02));color:var(--text);font-size:14px;text-align:center}
    .overlay{position:fixed;inset:0;background:rgba(10,8,6,.72);backdrop-filter:blur(10px);display:none;place-items:center;padding:20px;z-index:40}
    .overlay.open{display:grid}
    .result-card{width:min(92vw,560px);border-radius:28px;border:1px solid var(--border);background:linear-gradient(180deg,#231c17 0%,#171210 100%);box-shadow:var(--shadow);padding:28px;text-align:center}
    .result-card .mini{font-size:12px;letter-spacing:.22em;text-transform:uppercase;color:var(--gold);margin-bottom:10px}
    .result-card h3{font-family:var(--font-display);font-size:clamp(2.2rem,5vw,4.4rem);line-height:.92;margin-bottom:14px}
    .result-card p{font-size:16px;line-height:1.6;color:var(--muted);max-width:30ch;margin:0 auto 18px}
    .gift-name{display:inline-block;padding:14px 18px;border-radius:999px;border:1px solid rgba(215,176,122,.24);background:rgba(215,176,122,.08);font-size:18px;font-weight:700;color:var(--text)}
    .result-close{margin-top:22px;min-height:48px;border:none;border-radius:999px;padding:0 22px;background:rgba(255,255,255,.06);color:var(--text);cursor:pointer}
  </style>
</head>
<body>
  <main class="panel">
    <div class="wheel-wrap">
      <div class="pointer" aria-hidden="true"></div>
      <canvas id="wheel" width="1200" height="1200" aria-label="Колесо фортуны"></canvas>
      <div class="hub"><span>Friends<br>Talk</span></div>
    </div>
    <div class="spin-wrap"><button class="spin" id="spinBtn">Крутить колесо</button></div>
    <div class="prize-list">
      <h2>Призы для гостя</h2>
      <div class="chips" id="chips"></div>
    </div>
  </main>

  <div class="overlay" id="overlay">
    <div class="result-card">
      <div class="mini">Поздравляем</div>
      <h3 id="resultTitle">Ваш подарок</h3>
      <p id="resultText">Сегодня удача на вашей стороне — забирайте свой подарок от Friends Talk.</p>
      <div class="gift-name" id="giftName">Лимонад бесплатно</div>
      <div><button class="result-close" id="closeBtn">Закрыть</button></div>
    </div>
  </div>

  <script>
    const prizeItems = [
      { label:'Второй кальян в подарок', color:'#7b5230', congrats:'Поздравляем! Вам выпал второй кальян в подарок.' },
      { label:'Кальян в подарок', color:'#d4a36e', congrats:'Поздравляем! Вам выпал кальян в подарок.' },
      { label:'Депозит 500 ₽', color:'#8d5a33', congrats:'Поздравляем! Вам выпал депозит 500 ₽.' },
      { label:'Десерт в подарок', color:'#6c4a30', congrats:'Поздравляем! Вам выпал десерт в подарок.' },
      { label:'Лимонад бесплатно', color:'#c08a52', congrats:'Поздравляем! Вам выпал лимонад бесплатно.' }
    ];
    const fillerItems = [
      { label:'Звёзды сегодня молчат', color:'#2f2621' },
      { label:'Фортуна дремлет', color:'#352a24' },
      { label:'Почти поймали удачу', color:'#3a2c25' },
      { label:'Повезёт в следующий раз', color:'#332822' },
      { label:'Следующий ход будет сильнее', color:'#2b221d' }
    ];
    const items = [...prizeItems, ...fillerItems];
    const weights = [5,5,5,5,5,15,15,15,15,15];
    const chips = document.getElementById('chips');
    prizeItems.forEach(item => {
      const el = document.createElement('div');
      el.className = 'chip';
      el.textContent = item.label;
      chips.appendChild(el);
    });
    const canvas = document.getElementById('wheel');
    const ctx = canvas.getContext('2d');
    const spinBtn = document.getElementById('spinBtn');
    const overlay = document.getElementById('overlay');
    const resultTitle = document.getElementById('resultTitle');
    const resultText = document.getElementById('resultText');
    const giftName = document.getElementById('giftName');
    const closeBtn = document.getElementById('closeBtn');
    let rotation = -Math.PI / 2;
    let spinning = false;
    const seg = (Math.PI * 2) / items.length;

    function wrapText(text, x, y, maxWidth, lineHeight) {
      const words = text.split(' '); let line = ''; const lines = [];
      for (let n = 0; n < words.length; n++) {
        const testLine = line + words[n] + ' ';
        if (ctx.measureText(testLine).width > maxWidth && n > 0) { lines.push(line.trim()); line = words[n] + ' '; }
        else line = testLine;
      }
      lines.push(line.trim());
      const offset = ((lines.length - 1) * lineHeight) / 2;
      lines.forEach((l, i) => ctx.fillText(l, x, y - offset + i * lineHeight));
    }

    function drawWheel() {
      const cx = canvas.width / 2, cy = canvas.height / 2, radius = 530;
      ctx.clearRect(0,0,canvas.width,canvas.height);
      ctx.save(); ctx.translate(cx, cy); ctx.rotate(rotation);
      for (let i = 0; i < items.length; i++) {
        const start = i * seg, end = start + seg;
        ctx.beginPath(); ctx.moveTo(0,0); ctx.arc(0,0,radius,start,end); ctx.closePath(); ctx.fillStyle = items[i].color; ctx.fill();
        ctx.lineWidth = 8; ctx.strokeStyle = 'rgba(244,234,223,.18)'; ctx.stroke();
        ctx.save(); ctx.rotate(start + seg / 2); ctx.translate(radius * 0.67, 0); ctx.rotate(Math.PI / 2);
        ctx.fillStyle = '#efe0d0'; ctx.font = (i < prizeItems.length ? '700' : '600') + ' 44px Inter'; ctx.textAlign = 'center'; ctx.textBaseline = 'middle';
        wrapText(items[i].label, 0, 0, 220, 48); ctx.restore();
      }
      ctx.beginPath(); ctx.arc(0,0,radius + 18,0,Math.PI*2); ctx.lineWidth = 26; ctx.strokeStyle = 'rgba(215,176,122,.9)'; ctx.stroke();
      ctx.beginPath(); ctx.arc(0,0,radius + 34,0,Math.PI*2); ctx.lineWidth = 6; ctx.strokeStyle = 'rgba(255,244,228,.14)'; ctx.stroke();
      ctx.restore();
    }

    function weightedIndex(arr) {
      const total = arr.reduce((a,b) => a + b, 0); let r = Math.random() * total;
      for (let i = 0; i < arr.length; i++) { if (r < arr[i]) return i; r -= arr[i]; }
      return arr.length - 1;
    }

    function easeOutQuint(t) { return 1 - Math.pow(1 - t, 5); }

    function normalizeAngle(angle) {
      return (angle % (Math.PI * 2) + Math.PI * 2) % (Math.PI * 2);
    }

    function getWinningIndex() {
      const pointerAngle = normalizeAngle(-Math.PI / 2 - rotation);
      return Math.floor(pointerAngle / seg) % items.length;
    }

    function showResult(item) {
      if (prizeItems.some(p => p.label === item.label)) {
        resultTitle.textContent = 'Поздравляем!';
        resultText.textContent = item.congrats;
        giftName.textContent = item.label;
      } else {
        resultTitle.textContent = 'Спасибо за игру';
        resultText.textContent = 'Сегодня без подарка, но удача может улыбнуться вам уже в следующий раз.';
        giftName.textContent = 'Попробуйте ещё раз';
      }
      overlay.classList.add('open');
    }

    function spin() {
      if (spinning) return;
      spinning = true; spinBtn.disabled = true;

      const target = weightedIndex(weights);
      const centerAngle = target * seg + seg / 2;
      const targetRotation = -Math.PI / 2 - centerAngle;
      const extraTurns = Math.PI * 2 * (5 + Math.random() * 2);
      const startRotation = rotation;
      const currentNormalized = normalizeAngle(startRotation);
      const targetNormalized = normalizeAngle(targetRotation);
      const delta = ((targetNormalized - currentNormalized) + Math.PI * 2) % (Math.PI * 2);
      const finalRotation = startRotation + extraTurns + delta;
      const duration = 5200 + Math.random() * 1200;
      const start = performance.now();

      function frame(now) {
        const t = Math.min((now - start) / duration, 1);
        rotation = startRotation + (finalRotation - startRotation) * easeOutQuint(t);
        drawWheel();
        if (t < 1) {
          requestAnimationFrame(frame);
        } else {
          rotation = normalizeAngle(finalRotation);
          drawWheel();
          spinning = false;
          spinBtn.disabled = false;
          const actualIndex = getWinningIndex();
          showResult(items[actualIndex]);
        }
      }
      requestAnimationFrame(frame);
    }

    spinBtn.addEventListener('click', spin);
    closeBtn.addEventListener('click', () => overlay.classList.remove('open'));
    overlay.addEventListener('click', e => { if (e.target === overlay) overlay.classList.remove('open'); });
    drawWheel();
  </script>
</body>
</html>
