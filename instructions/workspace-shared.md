# Regole e Convenzioni Condivise del Workspace

Queste istruzioni definiscono il comportamento standard per gli agenti OpenCode che operano sui progetti utilizzando le skill di questo repository (`setup-workspace-env`, `slack-connector`, `gmail-imap`, `fellow-notes`).

## 1. Sicurezza e Credenziali (`.env`)
- Tutte le credenziali risiedono nel file `.env` (nella root del progetto corrente oppure centralizzato in `~/.config/opencode/.env`).
- **Non stampare mai in chiaro** nei messaggi o nei log i valori di `SLACK_XOXC_TOKEN`, `SLACK_XOXD_COOKIE`, `GMAIL_APP_PASSWORD` o `FELLOW_API_TOKEN`.
- Non committare mai file `.env` su Git.
- Se `.env` manca o un'API restituisce errore di autenticazione (`invalid_auth`, login IMAP fallito, `401 Unauthorized`), carica e segui immediatamente la skill `setup-workspace-env`.

## 2. Regole di Interazione con Servizi Esterni
- **Slack (`slack-connector`)**:
  - La lettura di canali, DM, MPIM e thread è sempre consentita.
  - **Conferma obbligatoria per l'invio**: prima di inviare qualsiasi messaggio (`chat.postMessage`), mostra sempre all'utente il destinatario e il testo esatto e attendi conferma esplicita.
- **Gmail (`gmail-imap`)**:
  - Accedi alle caselle IMAP esclusivamente in modalità **sola lettura** (`readonly=True`).
  - Non inviare email via SMTP senza richiesta e approvazione esplicita dell'utente.
- **Fellow (`fellow-notes`)**:
  - La ricerca e lettura di note, agende e action item è sempre consentita.
  - Chiedi conferma esplicita prima di sovrascrivere un'intera agenda o archiviare/completare action item, salvo istruzione diretta dell'utente.

## 3. Gestione Allegati e Documenti
- Salva gli allegati scaricati da Slack o Gmail nella cartella di progetto `allegati/` (se previsto dal progetto) usando la convenzione:
  `yyyymmdd-nomefile-[presentazione|trascrizione|note|altro].est`
- Per i documenti ricevuti in formati Office (`.pptx`, `.docx`, `.xlsx`), verifica sempre se è disponibile o generabile anche la controparte `.pdf` per facilitarne la consultazione.
- Nelle note, nei file Markdown e nelle risposte usa sempre **percorsi relativi** alla cartella di lavoro del progetto.

## 4. Operazioni Git e GitHub nel Container (`gh` CLI e HTTPS — No SSH / No GPG)
- All'interno del container è già preinstallato e autenticato il client **`gh`** (GitHub CLI tramite la variabile `GH_TOKEN`), mentre i binari `ssh` e `gpg` **non** sono presenti e **non** devono essere installati.
- Per verificare la connessione a GitHub o interagire con repository, issue, PR e API, usa sempre **`gh`** (`gh auth status`, `gh repo ...`, `gh pr ...`, `gh api ...`) e non tentare mai connessioni `ssh -T git@github.com`.
- Per `git commit` (se il `.gitconfig` dell'host ha `commit.gpgsign=true` attivo): usa `-c commit.gpgsign=false`:
  ```bash
  git -c safe.directory="$PWD" -c commit.gpgsign=false commit -m "Messaggio di commit"
  ```
- Per `git push`, `git pull` o `git fetch`:
  - Se il remote `origin` è configurato con un URL SSH (`git@github.com:<owner>/<repo>.git`) o se Git segnala `dubious ownership` (con `/home/opencode/.gitconfig` in sola lettura), esegui i comandi Git passando esplicitamente `safe.directory` e il credential helper di `gh` su URL **HTTPS**:
    ```bash
    git -c safe.directory="$PWD" -c credential.helper='!gh auth git-credential' push https://github.com/<owner>/<repo>.git <branch>
    ```
    oppure aggiorna l'URL del remote locale a HTTPS con:
    ```bash
    git -c safe.directory="$PWD" remote set-url origin https://github.com/<owner>/<repo>.git
    ```

## 5. Prevenzione Timeout `HTTP 504` (Ricerche Web Massive e Scrittura Documenti Corposi)
Per evitare errori **`HTTP 504 Gateway Timeout`** sul gateway LLM (causati dalla somma tra un prompt di input troppo pesante e una generazione di output molto lunga nello stesso turno):
1. **Usa sempre `format: "markdown"` in `webfetch`:** evita `format: "html"` (salvo stretta necessità tecnica), riducendo di circa 10 volte i token caricati nel contesto.
2. **Delega le letture estese a un `subagent` (`explore` o `general`):** quando devi consultare più di 4–5 pagine web esterne (`webfetch`) o molti file lunghi per raccogliere informazioni di contesto, delega la raccolta e l'analisi a un `subagent` che restituisca alla sessione principale solo una sintesi strutturata, mantenendo leggero il contesto principale e basso il *Time-To-First-Token (TTFT)*.
3. **Scrittura incrementale (`write` + `edit`) per documenti lunghi:** quando devi redigere un documento corposo (es. PRD, BRD, report completi oltre 1.500–2.000 parole), **non generare mai l'intero testo in un'unica chiamata `write`**. Crea prima la struttura/scheletro con `write` e compila poi le sezioni principali in passaggi successivi con `edit`.
