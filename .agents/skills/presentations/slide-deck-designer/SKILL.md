---
name: slide-deck-designer
description: Progetta, struttura e realizza presentazioni e slide deck HTML/PDF ad alto impatto, ariosi e leggibili, evitando muri di testo e tabelle sovraccariche. Usa questa skill ogni volta che l'utente chiede di "creare una slide", "fare una presentazione", "preparare un deck", "mappare un'architettura/processo in slide", "rendere una slide più digeribile/leggibile" o generare un template/presentazione stampabile in PDF A4 (es. per caroselli Slack, Demo Day, sync di team o pitch). Funziona in italiano e in inglese. Combina i principi di Assertion-Evidence, gestione del carico cognitivo, pattern compositivi (card grid, split architecture, swap rows) e un motore HTML zero-dipendenze navigabile da tastiera e pronto per la stampa PDF A4.
---

# Slide Deck Designer — Editorial & Assertion-Evidence Presentations

Questa skill trasforma appunti, architetture tecniche, roadmap o concetti complessi in **slide e mini-deck chiari, ariosi e immediatamente digeribili**, progettati sia per essere presentati a schermo (nel browser con le frecce `←` `→`) sia per essere esportati con `⌘ Cmd + P` in **PDF A4 perfetti** (che su Slack diventano automaticamente caroselli sfogliabili).

*(Ispirata e sintetizzata a partire dalle migliori pratiche di `wbohanw/html-presentation`, `blisse-code/presentation-design-skill` ed `ericmjl/skills/html-presentations`).*

---

## 1. Filosofia e Regole Auree (Non Negoziabili)

Il fallimento n°1 delle slide generate dall'AI è il **"Data Dump / Wall of Text"**: comprimere 5 domande diverse in una singola matrice da 30 celle di testo minuto. Una slide non è un documento Word né un foglio Excel.

Segui sempre queste **5 regole auree**:

1. **Un solo messaggio per slide (*One Concept Per Slide*):**
   - Ogni slide risponde a **una sola domanda** per chi guarda.
   - Se l'utente chiede "una slide" che però contiene **3 domande distinte** (es. *1. Cosa sono i componenti e a cosa servono?*, *2. Come si incastrano tra loro?*, *3. Quali sono le alternative per gli esperti?*), **proponi o genera un mini-deck progressivo di 2–4 slide** anziché stipare tutto in un unico foglio illeggibile.

2. **Ordine Narrativo per chi parte da zero (*Definizioni → Incastri → Eccezioni*):**
   - Mettiti sempre nei panni di chi apre la slide senza contesto pregresso:
     - **Slide 1 (Cosa sono & A quale domanda rispondono):** definisci prima gli elementi con parole semplici, la domanda a cui rispondono e la scelta standard.
     - **Slide 2 (Come funzionano / Come si incastrano):** mostra le relazioni, i macro-blocchi e il "contratto" che li tiene insieme.
     - **Slide 3 (Alternative / Confronto / Prossimi passi):** solo dopo aver chiarito lo standard, mostra cosa può essere sostituito o confrontato.

3. **Titoli Assertivi (*Assertion-Evidence*), mai etichette:**
   - **Errato (etichetta muta):** *"Architettura a 6 Layer"*
   - **Corretto (Eyebrow + Conclusione assertiva):**
     - Eyebrow: `TEAM HARNESS · COME FUNZIONA L'INCASTRO`
     - Titolo: *"I 6 layer lavorano in **3 blocchi** tenuti insieme da semplici file di testo."*
   - Evidenzia **una sola espressione chiave** (`<em>`) nel titolo: se evidenzi tutto, non stai evidenziando nulla.

4. **Limiti Rigidi di Densità Visiva (*Density Gate*):**
   - **Griglia di card:** massimo **6 card** per slide (layout `3×2` o `2×3`). Ogni card contiene al massimo: tag/numero, titolo breve, 1 frase di domanda/concetto (max 2 righe) e 1 box di risposta (max 2–3 righe).
   - **Righe di confronto / flusso:** massimo **5–6 righe** con colonne secche (max 8–12 parole per cella).
   - **Spazio bianco:** almeno il **25–30% della slide** deve restare libero per far respirare l'occhio.
   - **Mai tabelle a 5+ colonne piene di frasi:** sostituiscile sempre con i pattern compositivi in `references/composition-patterns.md`.

5. **Doppio Formato Nativo: Browser Interattivo + PDF Carosello Slack:**
   - Salvo richiesta diversa, produci sempre un singolo file `.html` zero-dipendenze (basato su `references/html-print-template.md`) accompagnato da una versione `.md` di sintesi:
     - **Nel browser:** navigabile a schermo intero con frecce `←` `→`, barra spaziatrice e pallini di avanzamento.
     - **In stampa (`⌘ Cmd + P` → Salva come PDF):** il blocco `@media print` nasconde i controlli di navigazione e impagina automaticamente ogni `<section class="slide">` su una pagina `A4 landscape` (o `A4 portrait` se si tratta di una scheda/traccia one-page).
     - **Su Slack:** ricorda all'utente che caricando un PDF multi-pagina orizzontale su Slack, Slack genera in automatico il **carosello sfogliabile direttamente in chat**.

---

## 2. Workflow Operativo

### Passo 1 — Diagnosi rapida del contenuto e del formato
Prima di scrivere il codice HTML, identifica:
1. **Obiettivo e pubblico:** è per colleghi non-tech, sviluppatori, leadership o tutto il dipartimento?
2. **Tipo di artefatto:**
   - **Presentazione / Mappa architetturale / Pitch:** usa il formato **Slide Orizzontali (`A4 landscape`)** da 1 a N slide (`references/html-print-template.md`).
   - **Scheda operativa / Template compilabile / Traccia di lavoro:** usa il formato **Scheda Verticale One-Page (`A4 portrait`)**.
3. **Scomposizione del carico cognitivo:** conta quanti concetti distinti ci sono nella richiesta. Se superano i limiti di densità di una singola pagina, dividili in 2–4 slide con un filo logico progressivo (*1. Cosa sono → 2. Come si incastrano → 3. Alternative/Dettagli*).

### Passo 2 — Scelta dei Pattern Compositivi (vietati i muri di bullet point)
Consulta **`references/composition-patterns.md`** e scegli per ogni slide il pattern visivo più adatto:
- **Pattern 1 — *6-Card Question & Answer Grid (3×2)*:** ideale per mappare fino a 6 layer, componenti o pilastri (Mostra: *Numero + Definizione in 3 parole → Nome → Domanda a cui risponde → Scelta di Default*).
- **Pattern 2 — *Split Tower + High-Contrast Contract Panel*:** ideale per spiegare come più livelli si incastrano tra loro (a sinistra i 3 macro-blocchi impilati; a destra un pannello scuro ad alto contrasto che svela il "collante" o la regola d'oro).
- **Pattern 3 — *Swap Rows (`Default ⇄ Alternative`)*:** ideale per mostrare standard vs. eccezioni ("Paved Road vs. Bring Your Own Layer") riga per riga, con codifica colore distinta (blu per il default, verde acqua per le alternative).
- **Pattern 4 — *Hero Metric / Comparison Table*:** ideale per slide di impatto, ROI, prima/dopo (`AS-IS` vs `TO-BE` vs `Guadagno`).

### Passo 3 — Generazione HTML + Verifica di Qualità (Craft Checklist)
Genera il file `.html` seguendo lo scheletro in **`references/html-print-template.md`** e verifica:
- [ ] Ogni slide ha un **Eyebrow** (es. `TEAM HARNESS · MAPPA DELLO STACK`), un **Titolo assertivo** con una sola `<em>` evidenziata, un **Badge numero slide** (`Slide 1 / 3`) e un **Footer takeaway** con il messaggio chiave?
- [ ] Una persona che non conosce il progetto capisce la **Slide 1** senza dover prima leggere la Slide 2 o 3?
- [ ] Nessun blocco di testo supera le **3 righe** consecutive?
- [ ] I colori seguono una gerarchia pulita (sfondo chiaro `#f8fafc`, card bianche `#ffffff`, 1 colore primario `#0066cc`, 1 colore secondario `#0f766e` per i confronti, 1 pannello scuro `#0f172a` per i concetti cardine)?
- [ ] Il CSS include `@page { size: A4 landscape; margin: 0; }` e `@media print` con `page-break-after: always` così la stampa su PDF dal browser (`⌘ Cmd + P` con *"Grafica di sfondo"* attiva) produce pagine perfette senza tagli?

---

## 3. File di Riferimento della Skill

- **`references/composition-patterns.md`** — Catalogo dei 5 pattern visivi (HTML + CSS pronto all'uso) per sostituire elenchi puntati e tabelle pesanti. Leggilo sempre prima di progettare il layout delle slide.
- **`references/html-print-template.md`** — Template HTML/CSS/JS completo e testato per mini-deck interattivi a schermo e stampabili in PDF A4 landscape multi-pagina (o A4 portrait one-page).
