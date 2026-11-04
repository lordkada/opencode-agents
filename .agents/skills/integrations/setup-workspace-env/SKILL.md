---
name: Setup Credenziali Workspace (.env)
description: Guida interattiva passo-passo per creare, configurare e testare il file .env con le credenziali del workspace (Slack session token, Gmail App Password IMAP, Fellow API Key) e configurare il server MCP di Atlassian. Usa questa skill al primo avvio o quando un'integrazione restituisce errori di autenticazione.
---

# Skill: Setup Guidato Credenziali Workspace (`.env`) e Server MCP Atlassian

Procedura standard riutilizzabile in qualsiasi progetto OpenCode (sia in esecuzione nativa, sia in esecuzione containerizzata Docker / Rancher Desktop) per accompagnare l'utente nella configurazione iniziale o nel rinnovo delle credenziali **Slack**, **Gmail**, **Fellow** e del server **Atlassian MCP** (`https://mcp.atlassian.com/v2/mcp`).

## Quando usare questa skill
1. L'utente chiede di fare il **setup iniziale**, **configurare il progetto**, **configurare `.env`**, **configurare Atlassian MCP** o **aggiornare i token** (o esegue `/setup-env`).
2. Il file `.env` non esiste né nella cartella di progetto (`./.env`) né a livello globale (`~/.config/opencode/.env`).
3. Una delle integrazioni (`slack-connector`, `gmail-imap`, `fellow-notes`, `atlassian-mcp`) fallisce l'autenticazione perché un valore in `.env` è un placeholder (`inserisci-qui...`, `nome.cognome@example.com`, `xxxx...`), è scaduto (`invalid_auth`), oppure il server MCP Atlassian richiede autenticazione.

## Regole di sicurezza
- **Non stampare mai in chiaro** token o password già presenti in `.env`.
- Se il file `.env` viene salvato nella cartella del progetto (`./.env`), verifica sempre che `.gitignore` contenga `.env` così che nessun secret possa essere committato per errore su Git.
- Procedi **un passo alla volta** (verifica stato e scelta percorso → Slack → Gmail → Fellow → Atlassian MCP), attendendo la risposta dell'utente tra un passo e l'altro e testando subito ogni integrazione appena configurata.

---

## Architettura dei percorsi `.env` (Nativo vs. Container Docker)

Tutte le skill cercano il file `.env` in questo ordine di priorità:
1. `./.env` — Nella cartella del progetto corrente (visibile identicamente sia sul Mac dell'utente sia dentro il container grazie al mount `-v "$PWD":"$PWD"`).
2. `/work/.env` — Eventuale mount legacy container.
3. `~/.config/opencode/.env` — Configurazione globale condivisa tra tutti i progetti OpenCode:
   - **Nota importante per chi usa OpenCode in container (`opencode-as-a-container`):** dentro il container `~` corrisponde a `/home/opencode` (quindi `/home/opencode/.config/opencode/.env`), che sul Mac dell'utente (host) è mappato in **`~/.local/share/opencode-container/opencode-data/.config/opencode/.env`**.
   - **Attenzione a non confondere i file:** il file `~/.local/share/opencode-container/.env` sul Mac appartiene esclusivamente al motore del container (`LLM_API_KEY` e `GH_TOKEN`, gestiti da `opencode --container-config`) e **non** deve essere usato per i token delle skill (Slack/Gmail/Fellow).

### Helper Python standard per individuare e leggere `.env`
Usa sempre questa funzione in tutti i passaggi di verifica e test:

```python
import os

DEFAULT_ENV_TEMPLATE = """# ==============================================================================
# Credenziali Workspace per OpenCode (Slack, Gmail, Fellow)
# ==============================================================================
# ATTENZIONE: Non committare MAI questo file su Git!
# ==============================================================================

# 1. Slack - usati dalla skill `slack-connector`
SLACK_XOXC_TOKEN="xoxc-inserisci-qui-il-tuo-token"
SLACK_XOXD_COOKIE="xoxd-inserisci-qui-il-tuo-cookie"

# 2. Gmail Google Workspace (IMAP sola lettura) - usati dalla skill `gmail-imap`
GMAIL_USER="nome.cognome@example.com"
GMAIL_APP_PASSWORD="xxxx xxxx xxxx xxxx"

# 3. Fellow (Meeting Notes & Action Items) - usati dalla skill `fellow-notes`
FELLOW_SUBDOMAIN="inserisci-qui-il-sottodominio"
FELLOW_API_TOKEN="inserisci-qui-il-tuo-token-fellow"

# 4. Atlassian MCP Server (https://mcp.atlassian.com/v2/mcp) - OPZIONALE
# Di default usa OAuth 2.1 tramite `/mcps` in OpenCode senza variabili .env.
# Imposta solo se usi autenticazione via Personal API Token (Basic base64 email:token) con `"oauth": false`:
ATLASSIAN_MCP_BASIC_AUTH="inserisci-qui-base64-email-e-token"
"""

def find_env_path():
    for candidate in [".env", "/work/.env", os.path.expanduser("~/.config/opencode/.env")]:
        if os.path.exists(candidate):
            return candidate
    return None

def load_env():
    env = {}
    path = find_env_path()
    if path and os.path.exists(path):
        with open(path) as f:
            for line in f:
                line = line.strip()
                if line and not line.startswith("#") and "=" in line:
                    k, v = line.split("=", 1)
                    env[k.strip()] = v.strip().strip('"').strip("'")
    return path, env
```

---

## Sequenza di Setup Guidato (Passo-Passo)

> 💡 **Tip rapido Copia/Incolla su Mac (Terminale e Obsidian):**
> Quando esegui OpenCode dentro il container Docker su macOS, la selezione del mouse nella TUI mostra il popup *"Copied to clipboard"* ma non copia realmente negli appunti di sistema del Mac.
> Ricorda subito all'utente questo trucco semplicissimo (valido sia nel **Terminale Mac** sia dentro **Obsidian**):
> - Con la finestra del Terminale (o di Obsidian) attiva, premi **`Cmd + R`** *(menu **Vista → Consenti resoconto mouse / Allow Mouse Reporting**)* per disabilitare la cattura del mouse e usare normalmente la selezione nativa e `Cmd + C` / `Cmd + V`;
> - *In alternativa*, tieni premuto il tasto **`fn`** mentre selezioni il testo col mouse, poi premi `Cmd + C`.

### Passo 0: Verifica iniziale dello stato di `.env` e di GitHub CLI (`gh`)

Esegui questo script Python per controllare se esiste già un file `.env` (di progetto o globale), quali credenziali sono già configurate e se il client GitHub (`gh`) preinstallato nel container è autenticato correttamente via HTTPS:

```python
import os, subprocess

def find_env_path():
    for candidate in [".env", "/work/.env", os.path.expanduser("~/.config/opencode/.env")]:
        if os.path.exists(candidate):
            return candidate
    return None

path = find_env_path()
env = {}
if path:
    with open(path) as f:
        for line in f:
            line = line.strip()
            if line and not line.startswith("#") and "=" in line:
                k, v = line.split("=", 1)
                env[k.strip()] = v.strip().strip('"').strip("'")

def is_set(key, placeholders):
    val = env.get(key, "")
    return bool(val) and not any(p in val for p in placeholders)

gh_ok = subprocess.run(["gh", "auth", "status"], capture_output=True, text=True).returncode == 0

print("PERCORSO_ENV:", path or "NESSUNO")
print("TEMPLATE_LOCALE_PRESENTE:", os.path.exists(".env.template"))
print("GITHUB CLI (gh via HTTPS) autenticato:", gh_ok)
print("SLACK configurato:", is_set("SLACK_XOXC_TOKEN", ["inserisci-qui"]) and is_set("SLACK_XOXD_COOKIE", ["inserisci-qui"]))
print("GMAIL configurato:", is_set("GMAIL_USER", ["nome.cognome@"]) and is_set("GMAIL_APP_PASSWORD", ["xxxx"]))
print("FELLOW configurato:", is_set("FELLOW_SUBDOMAIN", ["inserisci-qui"]) and is_set("FELLOW_API_TOKEN", ["inserisci-qui"]))
```
*(Nota: per verificare o usare GitHub dentro il container usa **sempre** il client `gh` preinstallato con protocollo HTTPS; non usare né installare mai il client `ssh`).*

#### Come comportarsi in base all'esito del Passo 0:
1. **Se `PERCORSO_ENV` è `NESSUNO`:**
   Chiedi all'utente dove preferisce creare il file `.env` (oppure crealo in `./.env` se siamo già in un repository di progetto con `.env.template`):
   - **Opzione 1 — Nella cartella di questo progetto (`./.env`):** comodissimo da aprire e modificare anche con qualsiasi editor sul Mac perché si trova direttamente nella cartella di lavoro corrente.
   - **Opzione 2 — Globale per tutti i progetti (`~/.config/opencode/.env`):** configurato una volta sola, vale automaticamente per tutte le cartelle che aprirai con OpenCode *(se l'utente usa il container, crea tu il file in `/home/opencode/.config/opencode/.env` così sul suo Mac sarà salvato automaticamente in `~/.local/share/opencode-container/opencode-data/.config/opencode/.env`)*.
   - Quando crei il file `.env`:
     - se esiste `.env.template` nella cartella corrente, copialo;
     - se `.env.template` **non** esiste in locale (es. progetto configurato in modalità 100% remota), scrivi nel file il contenuto di `DEFAULT_ENV_TEMPLATE` e imposta `chmod 600`.
     - se salvi in `./.env` e nella cartella esiste un `.gitignore` che non cita `.env`, aggiungi `.env` a `.gitignore`.
2. **Mostra il riepilogo dello stato** (✅ Configurato / ❌ Da configurare per Slack, Gmail e Fellow) e offri sempre all'utente **due modalità** per inserire i valori mancanti:
   - **Modalità A (assistita in chat — consigliata specialmente col container):** l'utente incolla i valori direttamente nella chat di OpenCode e l'agente aggiorna il file `.env` per lui nel percorso corretto.
   - **Modalità B (in editor sul Mac):** l'utente apre il file `.env` (nella cartella del progetto) con un editor di testo, incolla i valori, salva e scrive *"fatto"* in chat.

---

### Passo 1/3: Configurazione di Slack (`SLACK_XOXC_TOKEN` e `SLACK_XOXD_COOKIE`)

Spiega all'utente come estrarre i due token di sessione dal browser:
1. Apri **Slack nel browser** all'indirizzo `https://app.slack.com` (oppure `https://<tuo-workspace>.slack.com/home`, assicurandoti di essere autenticato nel tuo workspace di lavoro).
2. Apri gli **Strumenti per sviluppatori (DevTools)**:
   - Su **Mac:** premi `Cmd + Option + I` (oppure da menu Chrome: *Altri strumenti → Strumenti per sviluppatori*)
   - Su **Windows / Linux:** premi `F12` oppure `Ctrl + Shift + I`
3. **Per recuperare `SLACK_XOXC_TOKEN` (`xoxc-...`):**
   - Clicca sul tab **Console** dei DevTools.
   - Incolla questo comando e premi **Invio**:
     ```javascript
     Object.values(JSON.parse(localStorage.localConfig_v2).teams).map(t => ({name: t.name, token: t.token}))
     ```
     *(Nota importante: se Chrome mostra l'avviso giallo `Warning: Don't paste code into the DevTools Console...`, scrivi `allow pasting` nella Console e premi Invio, poi incolla di nuovo il comando sopra).*
   - Copia il valore `token` associato al tuo workspace (inizia con `xoxc-...`).
4. **Per recuperare `SLACK_XOXD_COOKIE` (`xoxd-...`):**
   - Sempre nei DevTools, clicca sul tab **Application** (o **Applicazione** in alto; se non lo vedi clicca su `>>`).
   - Nel menu a sinistra espandi **Cookies** e seleziona `https://app.slack.com` (o l'URL `https://<tuo-workspace>.slack.com`).
   - Nella tabella cerca la riga con **Name** uguale a **`d`**.
   - Fai doppio clic sul suo **Value** (una stringa lunga che inizia con `xoxd-...`) e copialo.

Appena l'utente fornisce i valori o conferma di averli salvati in `.env`, esegui subito il **test di verifica Slack**:
```python
import os, json, urllib.request, urllib.parse

def load_env():
    for candidate in [".env", "/work/.env", os.path.expanduser("~/.config/opencode/.env")]:
        if os.path.exists(candidate):
            env = {}
            with open(candidate) as f:
                for line in f:
                    line = line.strip()
                    if line and not line.startswith("#") and "=" in line:
                        k, v = line.split("=", 1)
                        env[k.strip()] = v.strip().strip('"').strip("'")
            return env
    raise FileNotFoundError("File .env non trovato")

env = load_env()
req = urllib.request.Request(
    "https://slack.com/api/auth.test",
    data=urllib.parse.urlencode({"token": env["SLACK_XOXC_TOKEN"]}).encode(),
    headers={
        "Cookie": f"d={env['SLACK_XOXD_COOKIE']}",
        "Content-Type": "application/x-www-form-urlencoded",
        "User-Agent": "Mozilla/5.0",
    },
    method="POST",
)
with urllib.request.urlopen(req) as resp:
    res = json.loads(resp.read().decode())
    print("OK:", res.get("ok"), "| User:", res.get("user"), "| Team:", res.get("team"), "| Error:", res.get("error"))
```

---

### Passo 2/3: Configurazione di Gmail (`GMAIL_USER` e `GMAIL_APP_PASSWORD`)

Spiega all'utente come generare una **Password per le app** di Google Workspace:
1. `GMAIL_USER`: il tuo indirizzo email Google Workspace (es. `nome.cognome@example.com`).
2. `GMAIL_APP_PASSWORD`:
   - Vai alla pagina del tuo account Google: **`https://myaccount.google.com/apppasswords`**
     *(Se non vedi la schermata, verifica su `https://myaccount.google.com/security` che la **Verifica in due passaggi** sia attiva).*
   - Nel campo **"Nome app"**, scrivi ad esempio `OpenCode` e clicca su **Crea**.
   - Copia il codice di **16 lettere** generato.

Appena configurato, esegui il **test di verifica Gmail IMAP in sola lettura**:
```python
import os, imaplib

def load_env():
    for candidate in [".env", "/work/.env", os.path.expanduser("~/.config/opencode/.env")]:
        if os.path.exists(candidate):
            env = {}
            with open(candidate) as f:
                for line in f:
                    line = line.strip()
                    if line and not line.startswith("#") and "=" in line:
                        k, v = line.split("=", 1)
                        env[k.strip()] = v.strip().strip('"').strip("'")
            return env
    raise FileNotFoundError("File .env non trovato")

env = load_env()
M = imaplib.IMAP4_SSL("imap.gmail.com", 993)
M.login(env["GMAIL_USER"], env["GMAIL_APP_PASSWORD"].replace(" ", ""))
typ, _ = M.select('"[Gmail]/Tutti i messaggi"', readonly=True)
print("IMAP Login OK | Cartella selezionata:", typ)
M.logout()
```

---

### Passo 3/3: Configurazione di Fellow (`FELLOW_SUBDOMAIN` e `FELLOW_API_TOKEN`)

Spiega all'utente come generare il token API di Fellow (opzionale se il progetto non usa Fellow):
1. Apri il tuo workspace Fellow nel browser (`https://<tuo-sottodominio>.fellow.app`).
2. Clicca sulla tua icona profilo → **User Settings** → **Developer** (o **API Keys**).
3. Clicca su **Create API Key**, assegna un nome (es. `OpenCode`) e copia il token generato.
4. Imposta `FELLOW_SUBDOMAIN=<tuo-sottodominio>` e `FELLOW_API_TOKEN=<il-tuo-token>`.

Appena configurato, esegui il **test di verifica Fellow API**:
```python
import os, urllib.request, json

def load_env():
    for candidate in [".env", "/work/.env", os.path.expanduser("~/.config/opencode/.env")]:
        if os.path.exists(candidate):
            env = {}
            with open(candidate) as f:
                for line in f:
                    line = line.strip()
                    if line and not line.startswith("#") and "=" in line:
                        k, v = line.split("=", 1)
                        env[k.strip()] = v.strip().strip('"').strip("'")
            return env
    raise FileNotFoundError("File .env non trovato")

env = load_env()
sub = env["FELLOW_SUBDOMAIN"]
req = urllib.request.Request(
    f"https://{sub}.fellow.app/api/v1/notes",
    data=json.dumps({"pagination": {"page_size": 1}}).encode("utf-8"),
    headers={
        "X-API-KEY": env["FELLOW_API_TOKEN"],
        "Content-Type": "application/json",
        "Accept": "application/json",
        "User-Agent": "Mozilla/5.0",
    },
    method="POST",
)
with urllib.request.urlopen(req) as resp:
    data = json.loads(resp.read().decode())
    print("Fellow API OK | Risposta ricevuta:", "notes" in data)
```

---

### Passo 4/4: Configurazione del Server MCP di Atlassian (`https://mcp.atlassian.com/v2/mcp`)

Spiega all'utente che il server MCP remoto ufficiale di Atlassian v2 ([guida ufficiale](https://support.atlassian.com/atlassian-ai-gateway/docs/get-started-with-the-atlassian-remote-mcp-server/)) consente a OpenCode di interagire con Jira, Confluence, Jira Service Management, Bitbucket, Loom e Teamwork Graph:

1. **Aggiunta del server MCP in OpenCode (`opencode.jsonc` o globale):**
   - Esegui:
     ```bash
     opencode mcp add atlassian --global --url https://mcp.atlassian.com/v2/mcp
     opencode mcp list
     ```
   - Oppure verifica che in `opencode.jsonc` (di progetto o globale in `~/.config/opencode/opencode.jsonc`) sia presente il blocco:
     ```jsonc
     "mcp": {
       "servers": {
         "atlassian": {
           "type": "remote",
           "url": "https://mcp.atlassian.com/v2/mcp"
         }
       }
     }
     ```
2. **Autenticazione:**
   - **Modalità consigliata (OAuth 2.1 interattivo):** invita l'utente ad aprire **`/mcps`** nella TUI di OpenCode, selezionare **`atlassian`** e completare l'accesso nel browser *(non lanciare `opencode mcp auth` dal tool `shell` in background)*.
   - **Modalità alternativa (Personal API Token / Basic Auth, utile in container Docker o ambienti headless):** se il redirect OAuth non è raggiungibile dal container e l'organizzazione ha abilitato i token API su Atlassian MCP, guida l'utente a generare un token su `https://id.atlassian.com/manage-profile/security/api-tokens?autofillToken&expiryDays=max&appId=mcp-v2&selectedScopes=all`, calcolare il Base64 di `email:api_token`, salvarlo in `ATLASSIAN_MCP_BASIC_AUTH` nel file `.env` e impostare `"oauth": false` e `"headers": { "Authorization": "Basic {env:ATLASSIAN_MCP_BASIC_AUTH}" }` nella configurazione del server `atlassian`.
