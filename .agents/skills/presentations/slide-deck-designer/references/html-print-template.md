# Template Base HTML + CSS Print-Ready (Schermo + PDF A4)

Usa questo scheletro quando generi una presentazione HTML multi-slide o una scheda one-page.

## Caratteristiche chiave del template
1. **Zero dipendenze esterne:** tutto in un unico file `.html` (nessun framework CDN che possa rompersi offline).
2. **Navigazione a schermo:** frecce tastiera (`←` / `→`), barra spaziatrice e barra di navigazione con indicatori cliccabili.
3. **Stampa PDF nativa (`⌘ Cmd + P`):**
   - `@page { size: A4 landscape; margin: 0; }` imposta automaticamente il formato orizzontale.
   - `print-color-adjust: exact` mantiene tutti gli sfondi, i badge e i pannelli colorati.
   - Ogni `<section class="slide">` diventa esattamente **una pagina A4** (`width: 297mm; height: 210mm; page-break-after: always;`) e nasconde la barra di navigazione `.nav-bar`.

---

## Scheletro Completo (`A4 Landscape` Multi-Slide)

```html
<!DOCTYPE html>
<html lang="it">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>TITOLO_PRESENTAZIONE</title>
  <style>
    :root {
      --bg: #f8fafc;
      --surface: #ffffff;
      --surface-dark: #0f172a;
      --ink: #0f172a;
      --muted: #64748b;
      --border: #e2e8f0;
      --accent: #0066cc;
      --accent-soft: #eff6ff;
      --accent-border: #bfdbfe;
      --dev: #0f766e;
      --dev-soft: #f0fdfa;
      --dev-border: #99f6e4;
      --font-sans: -apple-system, BlinkMacSystemFont, "Segoe UI", Inter, Roboto, Helvetica, Arial, sans-serif;
      --font-mono: "SFMono-Regular", ui-monospace, Consolas, Menlo, monospace;
    }

    * { box-sizing: border-box; margin: 0; padding: 0; }

    html, body {
      width: 100%;
      height: 100%;
      background: #0f172a;
      color: var(--ink);
      font-family: var(--font-sans);
      -webkit-print-color-adjust: exact !important;
      print-color-adjust: exact !important;
      overflow: hidden;
    }

    .deck { position: relative; width: 100vw; height: 100vh; }

    .slide {
      position: absolute;
      inset: 0;
      width: 100vw;
      height: 100vh;
      background: var(--bg);
      padding: 5.5vh 6.5vw 8vh 6.5vw;
      display: flex;
      flex-direction: column;
      justify-content: space-between;
      opacity: 0;
      pointer-events: none;
      transform: translateX(28px);
      transition: opacity 0.35s cubic-bezier(0.2, 0.8, 0.2, 1),
                  transform 0.35s cubic-bezier(0.2, 0.8, 0.2, 1);
    }

    .slide.active { opacity: 1; pointer-events: auto; transform: translateX(0); }
    .slide.prev { opacity: 0; transform: translateX(-28px); }

    /* Header */
    .slide-header {
      display: flex;
      justify-content: space-between;
      align-items: flex-start;
      gap: 2vw;
    }

    .eyebrow {
      display: inline-flex;
      align-items: center;
      gap: 8px;
      font-family: var(--font-mono);
      font-size: clamp(0.72rem, 0.95vw, 0.95rem);
      font-weight: 700;
      text-transform: uppercase;
      letter-spacing: 0.12em;
      color: var(--accent);
      margin-bottom: 1vh;
    }

    .eyebrow::before {
      content: "";
      display: inline-block;
      width: 8px;
      height: 8px;
      border-radius: 2px;
      background: var(--accent);
    }

    h1 {
      font-size: clamp(1.8rem, 3.1vw, 3rem);
      font-weight: 800;
      letter-spacing: -0.03em;
      line-height: 1.12;
      color: var(--ink);
      max-width: 64vw;
    }

    h1 em {
      font-style: normal;
      color: var(--accent);
      background: linear-gradient(180deg, transparent 62%, #dbeafe 62%);
      padding: 0 4px;
    }

    .slide-num-badge {
      font-family: var(--font-mono);
      font-size: clamp(0.75rem, 0.95vw, 0.95rem);
      color: var(--muted);
      background: var(--surface);
      border: 1px solid var(--border);
      padding: 6px 12px;
      border-radius: 999px;
      white-space: nowrap;
    }

    .slide-body {
      flex: 1;
      display: flex;
      flex-direction: column;
      justify-content: center;
      margin: 2.5vh 0;
    }

    .slide-footer {
      display: flex;
      align-items: center;
      justify-content: space-between;
      background: var(--surface);
      border: 1px solid var(--border);
      border-left: 5px solid var(--accent);
      border-radius: 10px;
      padding: 1.4vh 1.8vw;
      font-size: clamp(0.85rem, 1.1vw, 1.08rem);
      color: #334155;
      box-shadow: 0 2px 8px rgba(15, 23, 42, 0.03);
    }

    .code-pill {
      font-family: var(--font-mono);
      font-size: 0.9em;
      font-weight: 600;
      background: #f1f5f9;
      border: 1px solid #cbd5e1;
      color: #0f172a;
      padding: 2px 7px;
      border-radius: 5px;
    }

    /* Pattern A: 6-Card Grid (3x2) */
    .cards-6-grid { display: grid; grid-template-columns: repeat(3, 1fr); gap: 1.6vw; }
    .q-card {
      background: var(--surface);
      border: 1px solid var(--border);
      border-top: 5px solid var(--accent);
      border-radius: 14px;
      padding: 2.1vh 1.5vw;
      display: flex;
      flex-direction: column;
      justify-content: space-between;
      gap: 1.2vh;
      box-shadow: 0 4px 12px rgba(15, 23, 42, 0.04);
    }
    .q-card.top-tier { border-top-color: #2563eb; }
    .q-card.mid-tier { border-top-color: #0d9488; }
    .q-card.bot-tier { border-top-color: #475569; }
    .q-card-head { display: flex; justify-content: space-between; align-items: center; gap: 8px; }
    .layer-num {
      font-family: var(--font-mono); font-size: clamp(0.7rem, 0.85vw, 0.82rem);
      font-weight: 800; text-transform: uppercase; padding: 3px 8px; border-radius: 5px;
    }
    .top-tier .layer-num { background: #dbeafe; color: #1d4ed8; }
    .mid-tier .layer-num { background: #ccfbf1; color: #0f766e; }
    .bot-tier .layer-num { background: #e2e8f0; color: #334155; }
    .layer-def { font-size: clamp(0.74rem, 0.88vw, 0.85rem); font-weight: 700; color: var(--muted); }
    .layer-main-title { font-size: clamp(1.05rem, 1.32vw, 1.28rem); font-weight: 800; color: var(--ink); }
    .q-bubble { font-size: clamp(0.84rem, 1.02vw, 0.98rem); font-style: italic; color: #334155; line-height: 1.32; }
    .a-box { background: #f8fafc; border: 1px solid #e2e8f0; border-radius: 9px; padding: 1.1vh 1vw; }
    .a-box .a-tag { font-family: var(--font-mono); font-size: 0.68rem; font-weight: 700; text-transform: uppercase; color: var(--muted); display: block; margin-bottom: 2px; }
    .a-box .a-title { font-size: clamp(0.88rem, 1.08vw, 1.04rem); font-weight: 800; color: var(--accent); margin-bottom: 0.2vh; }
    .a-box .a-detail { font-size: clamp(0.75rem, 0.88vw, 0.86rem); color: #475569; line-height: 1.3; }

    /* Pattern B: Split Tower + Contract Panel */
    .s1-grid { display: grid; grid-template-columns: 1.15fr 0.85fr; gap: 3.2vw; align-items: stretch; }
    .stack-tower { display: flex; flex-direction: column; gap: 1.8vh; justify-content: center; }
    .tier-card {
      background: var(--surface); border: 1px solid var(--border); border-radius: 14px;
      padding: 2.2vh 2vw; display: grid; grid-template-columns: auto 1fr auto;
      align-items: center; gap: 1.5vw; box-shadow: 0 4px 14px rgba(15, 23, 42, 0.04);
    }
    .tier-card.tier-top { border-left: 7px solid #2563eb; }
    .tier-card.tier-mid { border-left: 7px solid #0d9488; }
    .tier-card.tier-bot { border-left: 7px solid #475569; }
    .tier-icon { width: 52px; height: 52px; border-radius: 12px; display: flex; align-items: center; justify-content: center; font-family: var(--font-mono); font-weight: 800; }
    .tier-top .tier-icon { background: #dbeafe; color: #1d4ed8; }
    .tier-mid .tier-icon { background: #ccfbf1; color: #0f766e; }
    .tier-bot .tier-icon { background: #e2e8f0; color: #334155; }
    .tier-main h3 { font-size: clamp(1.1rem, 1.5vw, 1.45rem); font-weight: 800; margin-bottom: 0.4vh; }
    .tier-main p { font-size: clamp(0.85rem, 1.05vw, 1.02rem); color: var(--muted); line-height: 1.35; }
    .tier-badges { display: flex; flex-direction: column; align-items: flex-end; gap: 6px; }
    .pill-default { background: var(--accent-soft); border: 1px solid var(--accent-border); color: #1e40af; font-size: 0.85rem; font-weight: 700; padding: 4px 10px; border-radius: 999px; white-space: nowrap; }
    .pill-sub { font-family: var(--font-mono); font-size: 0.75rem; color: var(--muted); }
    .contract-panel { background: var(--surface-dark); color: #f8fafc; border-radius: 16px; padding: 3vh 2.4vw; display: flex; flex-direction: column; justify-content: space-between; }
    .contract-panel .panel-tag { font-family: var(--font-mono); font-size: 0.8rem; text-transform: uppercase; letter-spacing: 0.12em; color: #38bdf8; margin-bottom: 0.8vh; }
    .contract-panel h2 { font-size: clamp(1.25rem, 1.8vw, 1.75rem); font-weight: 800; line-height: 1.2; margin-bottom: 1.8vh; color: #ffffff; }
    .contract-panel > p { font-size: clamp(0.85rem, 1.02vw, 1rem); color: #cbd5e1; line-height: 1.45; margin-bottom: 2.2vh; }
    .contract-files { display: flex; flex-direction: column; gap: 1.4vh; }
    .file-row { background: rgba(255, 255, 255, 0.06); border: 1px solid rgba(255, 255, 255, 0.14); border-radius: 10px; padding: 1.4vh 1.2vw; display: grid; grid-template-columns: 115px 1fr; align-items: center; gap: 1vw; }
    .file-name { font-family: var(--font-mono); font-weight: 700; color: #38bdf8; }
    .file-desc { font-size: 0.9rem; color: #e2e8f0; line-height: 1.3; }

    /* Pattern C: Swap Rows (Default vs Dev) */
    .byol-container { display: flex; flex-direction: column; gap: 1.3vh; }
    .byol-head { display: grid; grid-template-columns: 1.1fr 1.45fr 80px 1.45fr; padding: 0 1.4vw 0.6vh 1.4vw; font-family: var(--font-mono); font-size: 0.8rem; font-weight: 700; text-transform: uppercase; color: var(--muted); }
    .byol-row { display: grid; grid-template-columns: 1.1fr 1.45fr 80px 1.45fr; align-items: center; background: var(--surface); border: 1px solid var(--border); border-radius: 11px; padding: 1.35vh 1.4vw; }
    .byol-layer { display: flex; align-items: center; gap: 10px; }
    .byol-layer .l-badge { font-family: var(--font-mono); font-size: 0.78rem; font-weight: 800; width: 32px; height: 26px; display: flex; align-items: center; justify-content: center; border-radius: 6px; background: #e2e8f0; }
    .byol-layer .l-name { font-weight: 800; font-size: 1rem; }
    .byol-default { background: var(--accent-soft); border: 1px solid var(--accent-border); color: #1e3a8a; padding: 0.9vh 1vw; border-radius: 8px; font-size: 0.92rem; }
    .byol-swap { text-align: center; font-family: var(--font-mono); font-weight: 700; color: var(--muted); }
    .byol-dev { background: var(--dev-soft); border: 1px solid var(--dev-border); color: #134e4a; padding: 0.9vh 1vw; border-radius: 8px; font-size: 0.92rem; }

    /* Bottom Navigation Bar */
    .nav-bar {
      position: fixed; bottom: 2vh; left: 50%; transform: translateX(-50%);
      display: flex; align-items: center; gap: 14px; background: rgba(15, 23, 42, 0.9);
      color: #ffffff; padding: 7px 16px; border-radius: 999px; z-index: 100;
    }
    .nav-btn { background: transparent; border: none; color: #cbd5e1; cursor: pointer; font-family: var(--font-mono); font-size: 0.82rem; padding: 3px 8px; }
    .dots { display: flex; gap: 8px; }
    .dot { width: 9px; height: 9px; border-radius: 999px; background: #475569; cursor: pointer; transition: all 0.25s ease; }
    .dot.active { width: 28px; background: #38bdf8; }

    /* Print Mode (A4 Landscape PDF) */
    @page { size: A4 landscape; margin: 0; }
    @media print {
      html, body { background: var(--bg); overflow: visible; height: auto; }
      .deck { width: 100%; height: auto; }
      .slide {
        position: relative; width: 297mm; height: 210mm; padding: 12mm 15mm;
        opacity: 1 !important; pointer-events: auto !important; transform: none !important;
        page-break-after: always; break-after: page;
      }
      .slide:last-child { page-break-after: auto; break-after: auto; }
      .nav-bar { display: none !important; }
    }
  </style>
</head>
<body>
  <main class="deck">
    <!-- Inserisci qui le <section class="slide"> -->
  </main>

  <nav class="nav-bar" aria-label="Controlli presentazione">
    <button class="nav-btn" id="prevBtn">← Prec</button>
    <div class="dots" id="dots"></div>
    <button class="nav-btn" id="nextBtn">Succ →</button>
  </nav>

  <script>
    (function () {
      const slides = Array.from(document.querySelectorAll('.slide'));
      const dotsContainer = document.getElementById('dots');
      let current = 0;
      slides.forEach((_, idx) => {
        const dot = document.createElement('div');
        dot.className = 'dot' + (idx === 0 ? ' active' : '');
        dot.addEventListener('click', () => goTo(idx));
        dotsContainer.appendChild(dot);
      });
      const dots = Array.from(dotsContainer.querySelectorAll('.dot'));
      function goTo(index) {
        if (index < 0 || index >= slides.length) return;
        current = index;
        slides.forEach((s, i) => {
          s.classList.remove('active', 'prev');
          if (i === current) s.classList.add('active');
          else if (i < current) s.classList.add('prev');
        });
        dots.forEach((d, i) => d.classList.toggle('active', i === current));
      }
      document.getElementById('prevBtn').addEventListener('click', () => goTo(current - 1));
      document.getElementById('nextBtn').addEventListener('click', () => goTo(current + 1));
      document.addEventListener('keydown', (e) => {
        if (e.key === 'ArrowRight' || e.key === ' ' || e.key === 'PageDown') { e.preventDefault(); goTo(current + 1); }
        else if (e.key === 'ArrowLeft' || e.key === 'PageUp') { e.preventDefault(); goTo(current - 1); }
      });
    })();
  </script>
</body>
</html>
```
