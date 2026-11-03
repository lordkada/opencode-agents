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
