# OpenCode Agents & Skills

Repository centralizzato di **skill**, **istruzioni condivise**, **comandi** e **template di configurazione** per [OpenCode](https://opencode.ai/v2/docs/), pensato per essere riutilizzato trasversalmente su più progetti.

Le skill sono organizzate per **tipologia** (`integrations`, `product-management`, `presentations`), così in ogni progetto puoi decidere se importarle tutte oppure attivare solo i gruppi che ti servono.

---

## 📦 Contenuto del Repository

```text
.
├── .agents/
│   └── skills/
│       ├── integrations/                      # Connettori verso tool di collaborazione (Slack, Gmail, Fellow)
│       │   ├── index.json                     # Catalogo HTTP per la categoria integrations
│       │   ├── setup-workspace-env/SKILL.md   # Guida interattiva per creare e testare il file .env
│       │   ├── slack-connector/SKILL.md       # Connettore Slack Web API (lettura, thread, file, invio)
│       │   ├── gmail-imap/SKILL.md            # Connettore Gmail IMAP sola lettura (X-GM-RAW) + allegati
│       │   └── fellow-notes/SKILL.md          # Connettore Fellow API v1 (note, agende, action items)
│       ├── product-management/                # Skill per Product Management
│       │   ├── index.json                     # Catalogo HTTP per la categoria product-management
│       │   ├── one-pager-writer/              # Scrittura e pressure-test di One-Pager / Executive Brief
│       │   │   ├── SKILL.md
│       │   │   └── references/
│       │   │       ├── framework-library.md
│       │   │       └── impact-craft.md
│       │   └── prd-writer/                    # Scrittura e revisione di PRD (Product Requirement Documents)
│       │       ├── SKILL.md
│       │       └── references/
│       │           ├── prd-template.md
│       │           └── quality-bar.md
│       └── presentations/                     # Skill per design e generazione di slide e presentazioni
│           ├── index.json                     # Catalogo HTTP per la categoria presentations
│           └── slide-deck-designer/           # Progettazione di slide deck HTML/PDF (Assertion-Evidence)
│               ├── SKILL.md
│               └── references/
│                   ├── composition-patterns.md
│                   └── html-print-template.md
├── instructions/
│   └── workspace-shared.md                    # Regole condivise (sicurezza .env, conferme invio, allegati)
├── .opencode/
│   └── commands/
│       └── setup-env.md                       # Comando rapido /setup-env per avviare il wizard credenziali
├── .env.template                              # Template delle variabili d'ambiente richieste dalle skill
├── .gitignore                                 # Esclude .env, allegati temporanei e cache
├── AGENTS.md                                  # Istruzioni per la manutenzione di questo repository
└── opencode.jsonc                             # Configurazione OpenCode locale a questo repository
```

### Skill disponibili per categoria

#### 🔌 `integrations` (Connettori Workspace)
| ID Skill | Nome | Descrizione |
| :--- | :--- | :--- |
| `setup-workspace-env` | **Setup Credenziali Workspace (`.env`)** | Procedura guidata passo-passo per creare, configurare e testare `.env` con i token di Slack, Gmail e Fellow. |
| `slack-connector` | **Connettore Slack** | Legge canali, DM, MPIM, thread, scarica allegati e invia messaggi/reaction su Slack tramite session token (`xoxc` / `xoxd`). |
| `gmail-imap` | **Connettore Gmail IMAP** | Cerca e legge email Google Workspace in sola lettura tramite query native `X-GM-RAW` e scarica allegati. |
| `fellow-notes` | **Connettore Fellow API** | Legge e aggiorna note dei meeting, talking points (agenda) e action items su `*.fellow.app`. |

#### 📋 `product-management` (Product Management)
| ID Skill | Nome | Descrizione |
| :--- | :--- | :--- |
| `one-pager-writer` | **One-Pager Writer** | Guida i Product Manager nella creazione e nel pressure-test di One-Pager ed Executive Brief sintetici ed efficaci per stakeholder e decision-maker (IT/EN). |
| `prd-writer` | **PRD Writer** | Supporta i Product Manager nella stesura, strutturazione e revisione di Product Requirement Documents (PRD) completi per prodotti digitali (IT/EN). |

#### 🎨 `presentations` (Presentazioni e Slide Design)
| ID Skill | Nome | Descrizione |
| :--- | :--- | :--- |
| `slide-deck-designer` | **Slide Deck Designer** | Progetta e genera presentazioni e slide deck HTML/PDF ad alto impatto (principio *Assertion-Evidence*, gestione del carico cognitivo, pattern compositivi e stampa nativa su PDF A4 landscape per caroselli Slack). |

---

## 🔐 Configurazione Credenziali (`.env`)

Tutte le skill della categoria `integrations` cercano automaticamente il file `.env` in questo ordine:
1. `./.env` (nella root del singolo progetto in cui stai lavorando — visibile identicamente sia sul Mac sia dentro il container Docker grazie al mount `-v "$PWD":"$PWD"`)
2. `/work/.env` (eventuale mount container)
3. `~/.config/opencode/.env` (configurazione globale utente per non dover duplicare `.env` in ogni progetto)
   - **Nota per chi usa OpenCode in Container Docker (`opencode-as-a-container`):** dentro il container `~` corrisponde a `/home/opencode` (quindi `/home/opencode/.config/opencode/.env`), che sul Mac dell'utente corrisponde al percorso **`~/.local/share/opencode-container/opencode-data/.config/opencode/.env`**.
   - **Attenzione:** non modificare `~/.local/share/opencode-container/.env` sul Mac per inserire i token Slack/Gmail/Fellow, perché quel file è riservato al motore del container (`LLM_API_KEY` e `GH_TOKEN`) e viene rigenerato da `opencode --container-config`.

### Setup rapido (Consigliato: guidato dall'agente)
1. Avvia `opencode` nella cartella del tuo progetto e chiedi all'agente:
   > *"Usa la skill `@setup-workspace-env` per configurare e testare il file `.env`"*
   *(oppure esegui `/setup-env` se hai configurato il comando in `opencode.jsonc`).*
2. L'agente creerà automaticamente il file `.env` (anche se nella cartella non è presente `.env.template`) chiedendoti se salvarlo solo nel progetto corrente (`./.env`) o a livello globale (`~/.config/opencode/.env`), e ti guiderà passo-passo nel test di Slack, Gmail e Fellow.

### Setup manuale alternativo (da Terminale)
Se preferisci copiare manualmente `.env.template` prima di avviare OpenCode:
```bash
# Opzione A: specifico per il progetto corrente (funziona sia su OpenCode nativo che containerizzato)
cp .env.template /percorso/del/progetto/.env
chmod 600 /percorso/del/progetto/.env

# Opzione B1: globale per tutti i progetti (se usi OpenCode in container Docker)
cp .env.template ~/.local/share/opencode-container/opencode-data/.config/opencode/.env
chmod 600 ~/.local/share/opencode-container/opencode-data/.config/opencode/.env

# Opzione B2: globale per tutti i progetti (se usi OpenCode installato nativamente sull'host)
cp .env.template ~/.config/opencode/.env
chmod 600 ~/.config/opencode/.env
```

---

## 🚀 Come includere questo repository nei tuoi progetti OpenCode

OpenCode V2 supporta nativamente il caricamento di **skill** e **istruzioni** direttamente da **URL HTTPS remoti**, senza bisogno di clonare questo repository in locale.

Puoi inserire la configurazione:
- **Una sola volta a livello globale** in `~/.config/opencode/opencode.jsonc` (così tutti i tuoi progetti avranno automaticamente le skill).
- **Nel singolo progetto** in `opencode.jsonc` (o `.opencode/opencode.jsonc`), scegliendo anche solo le categorie di skill che ti interessano in quel progetto.

---

### ⭐ Modalità 1 (Consigliata): Uso 100% Remoto da GitHub Pubblico (Zero Clone)

Ogni categoria sotto `.agents/skills/<categoria>/` possiede il proprio file `index.json`. In questo modo puoi includere tutte le categorie oppure soltanto quelle rilevanti per lo specifico progetto:

```jsonc
{
  "$schema": "https://opencode.ai/config.json",
  "skills": [
    // Includi le categorie di skill che ti servono in questo progetto:
    "https://raw.githubusercontent.com/lordkada/opencode-agents/main/.agents/skills/integrations/",
    "https://raw.githubusercontent.com/lordkada/opencode-agents/main/.agents/skills/product-management/",
    "https://raw.githubusercontent.com/lordkada/opencode-agents/main/.agents/skills/presentations/"
  ],
  "instructions": [
    "https://raw.githubusercontent.com/lordkada/opencode-agents/main/instructions/workspace-shared.md"
  ]
}
```

#### Come funziona dietro le quinte:
1. All'avvio, OpenCode legge l'`index.json` di ciascuna categoria configurata.
2. Scarica automaticamente ogni `SKILL.md` e tutti i relativi file di supporto (es. `references/framework-library.md`, `references/prd-template.md`) mettendoli in cache.
3. Quando in futuro modificherai una skill su GitHub e incrementerai il numero `"version"` dentro `.agents/skills/<categoria>/index.json`, OpenCode rileverà la nuova versione e aggiornerà la cache automaticamente.

*(Opzionale)* Se vuoi che l'agente possa anche consultare l'intero repository (es. per copiare `.env.template`), puoi aggiungere anche `references`:
```jsonc
  "references": {
    "opencode-agents": {
      "repository": "lordkada/opencode-agents",
      "branch": "main",
      "description": "Repository condiviso di skill, script e template per OpenCode"
    }
  }
```

---

### Modalità 2: Repository Clonato in Locale (o Repo Privato)

Se preferisci avere il repository clonato in locale:

1. Clona il repository dentro `~/.config/opencode/opencode-agents` (cartella condivisa e visibile anche dentro eventuale container Docker di OpenCode):
   ```bash
   git clone git@github.com:lordkada/opencode-agents.git ~/.config/opencode/opencode-agents
   ```
2. Configura `opencode.jsonc` (in locale OpenCode esplora ricorsivamente le sottocartelle fino a 6 livelli, quindi puoi puntare direttamente a `.agents/skills` per caricare tutte le categorie, oppure a `.agents/skills/<categoria>` per caricarne solo alcune):
   ```jsonc
   {
     "$schema": "https://opencode.ai/config.json",
     "skills": [
       // Tutte le categorie insieme:
       "~/.config/opencode/opencode-agents/.agents/skills"
       // Oppure solo una categoria specifica:
       // "~/.config/opencode/opencode-agents/.agents/skills/integrations"
     ],
     "instructions": [
       "~/.config/opencode/opencode-agents/instructions/workspace-shared.md"
     ]
   }
   ```

---

### Modalità 3: Inclusione come Git Submodule nel progetto

Se vuoi fissare una versione specifica delle skill dentro un progetto condiviso con altri colleghi:

1. Nel progetto di destinazione:
   ```bash
   git submodule add https://github.com/lordkada/opencode-agents.git .opencode/opencode-agents
   ```
2. Nel file `opencode.jsonc` del progetto:
   ```jsonc
   {
     "$schema": "https://opencode.ai/config.json",
     "skills": [
       "./.opencode/opencode-agents/.agents/skills"
     ],
     "instructions": [
       ".opencode/opencode-agents/instructions/workspace-shared.md"
     ]
   }
   ```

---

## 📝 Come strutturare `AGENTS.md` nei singoli progetti

Poiché la logica tecnica di connessione a Slack, Gmail e Fellow e le regole generali di sicurezza sono già fornite da questo repository, l'`AGENTS.md` di ciascun progetto dovrà contenere **solo i riferimenti specifici di quel progetto** (canali, domini email, ID note Fellow).

Ecco un template consigliato per l'`AGENTS.md` dei tuoi progetti:

```markdown
# Istruzioni di Progetto: <Nome Progetto>

## Contesto
Breve descrizione del progetto, degli stakeholder e degli obiettivi.

## Riferimenti Slack (per la skill `slack-connector`)
- Canale principale progetto: `C0123456789` (`#nome-canale`)
- Gruppo / MPIM fornitore: `G0123456789`

## Riferimenti Email (per la skill `gmail-imap`)
- Domini / mittenti da monitorare: `"fornitore.it" OR "progetto-xyz"`
- Cartella salvataggio allegati: `allegati/`

## Riferimenti Fellow (per la skill `fellow-notes`)
- Serie meeting settimanale SAL: Note ID `xxxxx` / partecipanti `collega@example.com`
```

---

## 🔄 Come funziona il Refresh e la Cache delle risorse remote

OpenCode V2 non interroga GitHub a ogni singolo messaggio inviato in chat, ma gestisce cache e aggiornamenti in modo intelligente in base al tipo di risorsa:

### 1. `skills` via HTTP Catalog (`<categoria>/index.json`)
- **Quando controlla il remoto:** al caricamento della configurazione del progetto (avvio di OpenCode su quel progetto), quando modifichi/salvi `opencode.jsonc`, oppure quando esegui manualmente il comando **`/reload`** nella TUI.
- **Gestione della cache (`version`):** OpenCode scarica `index.json` della categoria e confronta il campo `"version"` di ogni skill con quello salvato nella sua cache locale:
  - Se `"version"` **non è cambiato**, OpenCode usa la copia locale in cache senza riscaricare i file `SKILL.md` e `references/*`.
  - Se modifichi una skill su GitHub, **ricordati sempre di incrementare `"version"`** (es. da `"1"` a `"2"`) dentro `.agents/skills/<categoria>/index.json` prima di fare `git push`. Al successivo avvio o `/reload`, OpenCode rileverà il cambio di versione e scaricherà i nuovi file.
  - *(Nota: la CDN di `raw.githubusercontent.com` può impiegare fino a ~5 minuti dopo un `git push` per propagare il file aggiornato).*

### 2. `instructions` via URL HTTPS (`instructions/workspace-shared.md`)
- **Quando controlla il remoto:** viene scaricato **una volta al caricamento della configurazione** del progetto (con timeout di 5 secondi) o quando lanci **`/reload`**.
- **Fallback offline:** non viene riscaricato a ogni prompt durante la sessione. Se GitHub è temporaneamente irraggiungibile, OpenCode mantiene in memoria l'ultima versione scaricata con successo.

### 3. `references` via Git (`"repository": "lordkada/opencode-agents"`)
- **Quando fa `git fetch`:** se usi anche il blocco `references`, OpenCode mantiene un clone automatico in `~/.local/share/opencode/repos/github.com/lordkada/opencode-agents` e controlla gli aggiornamenti in background quando avvii il progetto o invii un prompt, ma **al massimo una volta ogni 24 ore**.

### In sintesi: checklist quando aggiorni questo repository
1. Modifica o aggiungi le skill in `.agents/skills/<categoria>/<skill-id>/SKILL.md` (ed eventuali file in `references/`).
2. **Incrementa il campo `"version"`** della skill modificata dentro `.agents/skills/<categoria>/index.json` (assicurandoti che tutti i file della skill siano elencati nell'array `"files"`).
3. Esegui `git commit` e `git push`.
4. Se hai una sessione OpenCode già aperta su un altro progetto e vuoi recepire subito le modifiche senza riavviare, esegui **`/reload`** nella TUI di OpenCode (attendendo qualche minuto per l'aggiornamento della CDN di GitHub Raw).

---

## 🛠️ Aggiungere nuove Categorie o Skill a questo repository

1. Scegli la categoria esistente (es. `integrations`, `product-management`, `presentations`) o creane una nuova in `.agents/skills/<nuova-categoria>/`.
2. Crea la cartella della skill in `.agents/skills/<categoria>/<nuova-skill-id>/` (usa solo minuscole e trattini `kebab-case`).
3. Crea il file `.agents/skills/<categoria>/<nuova-skill-id>/SKILL.md` con il frontmatter YAML:
   ```markdown
   ---
   name: Nome Leggibile della Skill
   description: Spiega chiaramente cosa fa la skill e QUANDO l'agente deve usarla.
   ---

   # Istruzioni della Skill
   ...
   ```
4. Aggiorna (o crea) `.agents/skills/<categoria>/index.json` elencando tutti i file relativi alla skill nell'array `"files"` (es. `["SKILL.md", "references/guida.md"]`) e incrementando `"version"` in caso di modifiche successive.
5. Se la skill richiede nuove variabili d'ambiente, aggiungile con un placeholder in `.env.template` e aggiorna la skill `setup-workspace-env`.
