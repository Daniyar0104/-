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
      ctx.beginPath(); ctx.moveTo(0,0); ctx.arc(0,0,radius,start,end); ctx.closePath();
      ctx.fillStyle = items[i].color; ctx.fill();
      ctx.lineWidth = 8; ctx.strokeStyle = 'rgba(244,234,223,.18)'; ctx.stroke();

      ctx.save();
      ctx.rotate(start + seg / 2);
      ctx.translate(radius * 0.67, 0);
      ctx.rotate(Math.PI / 2);
      ctx.fillStyle = '#efe0d0';
      ctx.font = (i < prizeItems.length ? '700' : '600') + ' 44px Inter';
      ctx.textAlign = 'center'; ctx.textBaseline = 'middle';
      wrapText(items[i].label, 0, 0, 220, 48);
      ctx.restore();
    }
    ctx.beginPath(); ctx.arc(0,0,radius + 18,0,Math.PI*2);
    ctx.lineWidth = 26; ctx.strokeStyle = 'rgba(215,176,122,.9)'; ctx.stroke();
    ctx.beginPath(); ctx.arc(0,0,radius + 34,0,Math.PI*2);
    ctx.lineWidth = 6; ctx.strokeStyle = 'rgba(255,244,228,.14)'; ctx.stroke();
    ctx.restore();
  }

  function weightedIndex(arr) {
    const total = arr.reduce((a,b) => a + b, 0); let r = Math.random() * total;
    for (let i = 0; i < arr.length; i++) { if (r < arr[i]) return i; r -= arr[i]; }
    return arr.length - 1;
  }

  function easeOutQuint(t) { return 1 - Math.pow(1 - t, 5); }

  // НОВАЯ ФУНКЦИЯ: определяем индекс сегмента по текущему rotation
  function getIndexFromRotation() {
    // Стрелка смотрит вверх (−π/2), колесо повернуто на rotation
    let angle = -rotation - Math.PI / 2;
    angle = (angle % (Math.PI * 2) + Math.PI * 2) % (Math.PI * 2); // в [0, 2π)
    return Math.floor(angle / seg);
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

    // target оставляем для контроля вероятностей
    const target = weightedIndex(weights);
    const centerAngle = target * seg + seg / 2;
    const desired = -Math.PI / 2 - centerAngle;
    const extra = Math.PI * 2 * (5 + Math.random() * 2);
    const startRotation = rotation;
    const finalRotation = startRotation + extra + ((desired - startRotation) % (Math.PI * 2) + Math.PI * 2) % (Math.PI * 2);
    const duration = 5200 + Math.random() * 1200;
    const start = performance.now();

    function frame(now) {
      const t = Math.min((now - start) / duration, 1);
      rotation = startRotation + (finalRotation - startRotation) * easeOutQuint(t);
      drawWheel();
      if (t < 1) {
        requestAnimationFrame(frame);
      } else {
        rotation = finalRotation % (Math.PI * 2);
        drawWheel();
        spinning = false;
        spinBtn.disabled = false;

        // БЕРЁМ РЕАЛЬНЫЙ СЕКТОР ПОД СТРЕЛКОЙ
        const index = getIndexFromRotation();
        const item = items[index];
        showResult(item);
      }
    }
    requestAnimationFrame(frame);
  }

  spinBtn.addEventListener('click', spin);
  closeBtn.addEventListener('click', () => overlay.classList.remove('open'));
  overlay.addEventListener('click', e => { if (e.target === overlay) overlay.classList.remove('open'); });

  drawWheel();
</script>
