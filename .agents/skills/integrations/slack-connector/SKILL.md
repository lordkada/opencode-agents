---
name: Connettore Slack
description: Legge e invia messaggi, thread, reaction e scarica allegati su Slack tramite i session token salvati in .env. Usa questa skill ogni volta che devi controllare canali Slack, leggere chat dirette/MPIM o inviare comunicazioni su Slack.
---

# Skill: Integrazione Slack (Web API)

Istruzioni tecniche riutilizzabili per interagire con le API di **Slack** usando i token di sessione salvati in `.env`.
Per sapere **quali canali, DM o gruppi monitorare** nel progetto corrente, fai sempre riferimento a `AGENTS.md` nella root del repository.

## Configurazione e Sicurezza
Le credenziali sono lette da `.env` (o `~/.config/opencode/.env`):
- `SLACK_XOXC_TOKEN` (`xoxc-...`): inviato come parametro `token` nel body `application/x-www-form-urlencoded`.
- `SLACK_XOXD_COOKIE` (`xoxd-...`): inviato nell'header HTTP `Cookie: d=<SLACK_XOXD_COOKIE>`.

*(Se `.env` non esiste o restituisce `invalid_auth`, carica la skill `setup-workspace-env` per guidare l'utente nel rinnovo dei token).*

**Regole di sicurezza:**
- **Non stampare né committare mai** in chiaro i valori di `SLACK_XOXC_TOKEN` e `SLACK_XOXD_COOKIE`.
- **Conferma esplicita prima dell'invio (`chat.postMessage`):** chiedi sempre conferma esplicita all'utente mostrando destinatario e testo prima di inviare nuovi messaggi su Slack. Se devi inviare messaggi a più destinatari, procedi in modo sequenziale attendendo la conferma dopo ogni singolo invio.

---

## Client Python Standard per Slack Web API

```python
import os, json, urllib.request, urllib.parse

def load_env():
    env = {}
    for candidate in [".env", "/work/.env", os.path.expanduser("~/.config/opencode/.env")]:
        if os.path.exists(candidate):
            with open(candidate) as f:
                for line in f:
                    line = line.strip()
                    if line and not line.startswith("#") and "=" in line:
                        k, v = line.split("=", 1)
                        env[k.strip()] = v.strip().strip('"').strip("'")
            break
    return env

env = load_env()
token = env["SLACK_XOXC_TOKEN"]
cookie = env["SLACK_XOXD_COOKIE"]

def slack_api(method, params=None):
    data = {"token": token}
    if params:
        data.update(params)
    body = urllib.parse.urlencode(data).encode()
    req = urllib.request.Request(
        f"https://slack.com/api/{method}",
        data=body,
        headers={
            "Cookie": f"d={cookie}",
            "Content-Type": "application/x-www-form-urlencoded",
            "User-Agent": "Mozilla/5.0",
        },
        method="POST",
    )
    with urllib.request.urlopen(req) as resp:
        return json.loads(resp.read().decode())
```

---

## Operazioni Comuni

### 1. Leggere i messaggi di un canale, DM o MPIM (`conversations.history`)
E controllare sempre eventuali thread (`reply_count > 0`) con `conversations.replies`:
```python
res = slack_api("conversations.history", {"channel": CHANNEL_ID, "limit": 20})
for msg in res.get("messages", []):
    ts = msg.get("ts")
    user = msg.get("user")
    text = msg.get("text", "")
    print(f"[{ts}] {user}: {text}")
    if msg.get("reply_count", 0) > 0:
        replies = slack_api("conversations.replies", {"channel": CHANNEL_ID, "ts": ts})
        for r in replies.get("messages", [])[1:]:
            print(f"   ↳ [{r.get('ts')}] {r.get('user')}: {r.get('text', '')}")
```

### 2. Cercare messaggi o canali nel workspace (`search.messages` / `users.conversations`)
```python
# Ricerca testuale su Slack
found = slack_api("search.messages", {"query": "termine di ricerca", "count": 20})

# Elenco canali pubblici/privati/MPIM/IM a cui l'utente partecipa
convs = slack_api("users.conversations", {"types": "public_channel,private_channel,mpim,im", "limit": 200})
```

### 3. Scaricare file allegati condivisi su Slack
Quando un messaggio contiene `files`, scarica `url_private_download` passando l'header `Cookie: d=<SLACK_XOXD_COOKIE>` e `Authorization: Bearer <SLACK_XOXC_TOKEN>`:
```python
def download_slack_file(url, dest_path):
    req = urllib.request.Request(
        url,
        headers={
            "Cookie": f"d={cookie}",
            "Authorization": f"Bearer {token}",
            "User-Agent": "Mozilla/5.0",
        },
    )
    with urllib.request.urlopen(req) as resp, open(dest_path, "wb") as out:
        out.write(resp.read())
```

### 4. Aggiungere una reaction o inviare un messaggio
- **Reaction (`reactions.add`):**
  ```python
  slack_api("reactions.add", {"channel": CHANNEL_ID, "timestamp": MSG_TS, "name": "thumbsup"})
  ```
- **Inviare un messaggio (`chat.postMessage` — SOLO dopo conferma esplicita dell'utente):**
  ```python
  slack_api("chat.postMessage", {"channel": CHANNEL_ID, "text": TESTO, "thread_ts": OPTIONAL_THREAD_TS})
  ```
