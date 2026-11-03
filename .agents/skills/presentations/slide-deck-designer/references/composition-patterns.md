# Catalogo dei Pattern Compositivi (Composition Patterns)

Questo file definisce la **grammatica visiva** delle slide.
**Regola fondamentale:** ogni volta che saresti tentato di scrivere un elenco puntato o una tabella a 5 colonne, scegli invece uno di questi pattern strutturali.

---

## 1. Header Standard: Eyebrow + Titolo Assertivo + Badge Slide

Ogni slide si apre con una testata asimmetrica che dichiara il contesto (`eyebrow`), la conclusione (`h1` con una sola parola chiave in `<em>`) e il contatore della slide (`slide-num-badge`):

```html
<header class="slide-header">
  <div>
    <div class="eyebrow">Contesto · Sottotema</div>
    <h1>Frase completa che esprime <em>la conclusione chiave</em> della slide.</h1>
  </div>
  <div class="slide-num-badge">Slide 1 / 3 · Etichetta Breve</div>
</header>
```

**Regole:**
- L'`eyebrow` è in font monospace, maiuscolo, 2–5 parole.
- L'`h1` è una frase assertiva completa, mai un titolo generico ("Panoramica", "Architettura").
- Usa `<em>` su **una sola espressione** per guidare l'occhio.

---

## 2. Pattern A — Griglia 6 Card: Definizioni, Domande e Risposte (`3×2`)

**Quando usarlo:** nella **Slide 1** per presentare fino a 6 layer, pilastri, fasi o componenti a chi non conosce ancora il dominio.

**Perché funziona:** ogni card ha una gerarchia a 4 livelli scansionabile in 3 secondi:
1. **Badge numero + definizione lampo** (es. `Layer 6` · *Dove leggiamo e scriviamo*)
2. **Titolo del componente** (es. `UI & Second Brain Personale`)
3. **La domanda dell'utente** in corsivo (*"Dove lavoro sui miei appunti tutti i giorni?"*)
4. **Il box della risposta / scelta di default** con titolo in grassetto e 2 righe di spiegazione.

```html
<div class="cards-6-grid">
  <div class="q-card top-tier">
    <div class="q-card-head">
      <span class="layer-num">Layer 6</span>
      <span class="layer-def">Dove leggiamo e scriviamo</span>
    </div>
    <div class="layer-main-title">UI &amp; Second Brain Personale</div>
    <div class="q-bubble">"Dove lavoro sui miei documenti tutti i giorni senza vivere nel terminale?"</div>
    <div class="a-box">
      <span class="a-tag">Scelta di Default</span>
      <div class="a-title">Obsidian + Terminale Mac</div>
      <div class="a-detail">Vault locale organizzato col metodo PARA. Tu editi le note, l'agente opera sugli stessi file.</div>
    </div>
  </div>
  <!-- Ripeti fino a max 6 card (raggruppate per colore: top-tier, mid-tier, bot-tier) -->
</div>
```

---

## 3. Pattern B — Split Tower + Pannello "Contratto" ad Alto Contrasto (`60% / 40%`)

**Quando usarlo:** nella **Slide 2** (dopo aver definito i singoli pezzi nella Slide 1) per spiegare **come si incastrano tra loro** e qual è il principio architetturale che li tiene uniti.

**Perché funziona:**
- A **sinistra (60%)** raggruppa gli elementi in **3 macro-blocchi impilati** dall'alto in basso (superficie → motore → fondamenta).
- A **destra (40%)** usa un **pannello scuro (`#0f172a`)** che rompe la monotonia visiva e mette in risalto il "segreto dell'incastro" (es. i 3 file standard `.md`, `AGENTS.md`, `SKILL.md` o le 3 regole d'oro).

```html
<div class="s1-grid">
  <!-- Colonna sinistra: 3 Macro-Blocchi -->
  <div class="stack-tower">
    <div class="tier-card tier-top">
      <div class="tier-icon">L6–L5</div>
      <div class="tier-main">
        <h3>1. Spazio di Lavoro &amp; Metodo Condiviso</h3>
        <p>Spiegazione sintetica di come interagiscono i livelli superiori.</p>
      </div>
      <div class="tier-badges">
        <span class="pill-default">Obsidian + Repo Skill</span>
        <span class="pill-sub">Layer 6 + Layer 5</span>
      </div>
    </div>
    <!-- Altri 2 blocchi: tier-mid e tier-bot -->
  </div>

  <!-- Colonna destra: Pannello Scuro (Il Contratto / Il Collante) -->
  <aside class="contract-panel">
    <div>
      <div class="panel-tag">🔗 Il segreto dell'incastro</div>
      <h2>Il filesystem locale è l'unico "contratto" tra umano e AI.</h2>
      <p>Spiegazione in 2 righe del perché non c'è lock-in.</p>
    </div>
    <div class="contract-files">
      <div class="file-row">
        <span class="file-name">*.md</span>
        <span class="file-desc"><strong>I Contenuti:</strong> descrizione sintetica.</span>
      </div>
      <!-- Altre 2 righe -->
    </div>
  </aside>
</div>
```

---

## 4. Pattern C — Righe di Scambio (`Default ⇄ Alternative` / *Bring Your Own Layer*)

**Quando usarlo:** quando devi confrontare un percorso standard (*Paved Road*) con le alternative per sviluppatori/utenti avanzati, oppure uno stato *Prima (AS-IS)* vs *Dopo (TO-BE)*.

**Perché funziona:** evita le tabelle multi-colonna pesanti. Ogni riga ha 4 zone fisse: `[Badge + Nome] | [Box Blu: Default] | ⇄ | [Box Verde Acqua: Alternativa]`. L'occhio confronta immediatamente sinistra e destra.

```html
<div class="byol-container">
  <div class="byol-head">
    <div>Layer</div>
    <div>🎯 Default Dipartimento (Zero-Attrito)</div>
    <div style="text-align:center;">Swap</div>
    <div>🛠️ Alternative Dev / Power User</div>
  </div>

  <div class="byol-row">
    <div class="byol-layer">
      <span class="l-badge">L6</span>
      <span class="l-name">UI &amp; Editor</span>
    </div>
    <div class="byol-default">
      <strong>Obsidian</strong> + Terminale Mac (vault documentale)
    </div>
    <div class="byol-swap">⇄</div>
    <div class="byol-dev">
      <strong>VS Code, Cursor, Zed, JetBrains</strong> o puro Terminale CLI
    </div>
  </div>
  <!-- Max 5-6 righe -->
</div>
```

---

## 5. Pattern D — Footer "Takeaway" (La Frase da Portarsi a Casa)

Ogni slide si chiude in basso con una barra orizzontale che riassume il **"So What?"** (perché è importante o un esempio concreto):

```html
<footer class="slide-footer">
  <div>
    <strong>💡 Messaggio chiave:</strong> Frase sintetica che tira le somme della slide con un esempio pratico.
  </div>
  <div style="font-family:var(--font-mono); font-size:0.8rem; color:var(--muted);">
    Usa ← → per sfogliare · Cmd+P per PDF
  </div>
</footer>
```
