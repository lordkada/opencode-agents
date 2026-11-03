---
name: Connettore Fellow API
description: Legge e aggiorna le note dei meeting, i talking points (agenda) e gli action items su Fellow (*.fellow.app) tramite la Fellow Developer API v1. Usa questa skill quando devi consultare o modificare agende, appunti di riunioni o action item su Fellow.
---

# Skill: Integrazione Fellow (Meeting Notes, Agenda & Action Items)

Istruzioni tecniche riutilizzabili per interagire con **Fellow** (`<sottodominio>.fellow.app`) tramite le API ufficiali REST v1.
Per conoscere gli **utenti, i meeting ricorrenti e gli ID delle note** specifici del progetto corrente, consulta `AGENTS.md` nella root del repository.

## Configurazione e Sicurezza
Le credenziali sono lette da `.env` (o `~/.config/opencode/.env`):
- `FELLOW_SUBDOMAIN`: sottodominio del workspace Fellow (es. `mio-team` per `https://mio-team.fellow.app`)
- `FELLOW_API_TOKEN`: Personal API Key di Fellow (inviata nell'header `X-API-KEY`)

*(Se `.env` non esiste o la chiave non è valida, carica la skill `setup-workspace-env` per guidare l'utente nella configurazione).*

**Regole di sicurezza:**
- **Non stampare né committare mai** il valore di `FELLOW_API_TOKEN`.
- Prima di sovrascrivere un'agenda (`POST /api/v1/note/{note_id}/agenda`) o completare/archiviare action items su Fellow, **chiedi conferma esplicita all'utente** (a meno che l'utente non abbia già chiesto esplicitamente di procedere).

---

## Client Python Standard per Fellow Developer API (`https://<sottodominio>.fellow.app/api/v1`)

```python
import os, urllib.request, json

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
sub = env["FELLOW_SUBDOMAIN"]
tok = env["FELLOW_API_TOKEN"]

def fellow_api(method, path, body=None):
    data = json.dumps(body).encode("utf-8") if body is not None else None
    req = urllib.request.Request(
        f"https://{sub}.fellow.app{path}",
        data=data,
        headers={
            "X-API-KEY": tok,
            "Content-Type": "application/json",
            "Accept": "application/json",
            "User-Agent": "Mozilla/5.0",
        },
        method=method,
    )
    with urllib.request.urlopen(req) as resp:
        return json.loads(resp.read().decode())
```

---

## Operazioni Principali

### 1. Cercare e leggere le Note (`POST /api/v1/notes` e `GET /api/v1/note/{note_id}`)
Puoi filtrare per `event_attendees` (lista di email) o per `title`:
```python
res = fellow_api("POST", "/api/v1/notes", {
    "filters": {"event_attendees": ["collega@example.com"]},
    "include": {
        "event_attendees": True,
        "content_markdown": True,
        "content_fellow_markdown": True,
    },
    "pagination": {"page_size": 10},
})

# Oppure leggere direttamente una singola nota:
note = fellow_api("GET", f"/api/v1/note/{NOTE_ID}")
```

### 2. Scrivere o aggiornare l'Agenda di una Nota (`content_fellow_markdown`)
Il formato editabile di Fellow (`content_fellow_markdown`) usa:
- `- ( ) Testo talking point` per i talking point da discutere (`- (x) ` per quelli completati)
- `- [ ] Testo action item {assignees="email@example.com:not_done"}` per gli action item
- Endpoint disponibili:
  - `POST /api/v1/note/{note_id}/agenda` con `{"content_fellow_markdown": "..."}` (sostituisce l'intera agenda)
  - `POST /api/v1/note/{note_id}/agenda/append` con `{"content_fellow_markdown": "..."}`
  - `POST /api/v1/note/{note_id}/agenda/prepend` con `{"content_fellow_markdown": "..."}`
  - `POST /api/v1/note/{note_id}/agenda/find-replace` con `{"find": "...", "content_fellow_markdown": "..."}`

### 3. Elencare e completare gli Action Items
- **Lista Action Items (`POST /api/v1/action_items`):**
  - `filters.scope`: `"assigned_to_me"` oppure `"assigned_to_others"` (o omesso per tutti).
- **Segnare un Action Item come completato (`POST /api/v1/action_item/{action_item_id}/complete`):**
  ```python
  fellow_api("POST", f"/api/v1/action_item/{action_item_id}/complete", {"completed": True})
  ```
