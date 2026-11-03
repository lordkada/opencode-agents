---
name: Connettore Gmail IMAP
description: Interroga le email Google Workspace (in entrata e in uscita) tramite IMAP in sola lettura usando la sintassi di ricerca nativa di Gmail (X-GM-RAW) e scarica gli allegati. Usa questa skill quando devi controllare email, cercare comunicazioni con fornitori/colleghi o scaricare allegati da Gmail.
---

# Skill: Integrazione Gmail via IMAP (Google Workspace)

Istruzioni tecniche riutilizzabili per cercare e leggere email tramite **IMAP su Gmail** in modalità sola lettura (`readonly=True`).
Per sapere **quali mittenti, domini, oggetti o file di tracciamento usare** nel progetto corrente, fai sempre riferimento a `AGENTS.md` nella root del repository.

## Configurazione e Sicurezza
Le credenziali sono lette da `.env` (o `~/.config/opencode/.env`):
- `GMAIL_USER`: indirizzo email Google Workspace dell'utente (es. `nome.cognome@example.com`)
- `GMAIL_APP_PASSWORD`: App Password Google di 16 caratteri (`https://myaccount.google.com/apppasswords`)

*(Se `.env` non esiste o il login IMAP fallisce, carica la skill `setup-workspace-env` per guidare l'utente nella configurazione).*

**Regole di sicurezza:**
- **Non stampare né committare mai** i valori di `.env`.
- Aprire le cartelle IMAP **sempre in sola lettura** (`readonly=True`).
- Non inviare mai email via SMTP senza esplicita richiesta e conferma dell'utente sul testo e sui destinatari.

---

## Come interrogare Gmail via IMAP (`imaplib` + `X-GM-RAW`)

Per cercare tutte le email (sia ricevute che inviate) si seleziona la cartella `"[Gmail]/Tutti i messaggi"` con `readonly=True` e si usa l'estensione nativa `X-GM-RAW`, che accetta la **stessa identica sintassi della barra di ricerca di Gmail** (es. `"dominio.it"`, `"from:fornitore.it"`, `"subject:SAL"`, `"has:attachment after:2026/10/01"`).

```python
import os, imaplib, email
from email.header import decode_header

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
user = env["GMAIL_USER"]
pwd = env["GMAIL_APP_PASSWORD"].replace(" ", "")

M = imaplib.IMAP4_SSL("imap.gmail.com", 993)
M.login(user, pwd)
M.select('"[Gmail]/Tutti i messaggi"', readonly=True)

# Sostituire GMAIL_QUERY con la query di progetto (es. '"fornitore.it"')
GMAIL_QUERY = '"esempio.it"'
typ, data = M.uid("SEARCH", "X-GM-RAW", GMAIL_QUERY)
uids = data[0].split()

def decode_str(val):
    if not val:
        return ""
    parts = decode_header(val)
    return "".join(
        p.decode(enc or "utf-8", errors="replace") if isinstance(p, bytes) else p
        for p, enc in parts
    )

def get_text_body(msg):
    if msg.is_multipart():
        for part in msg.walk():
            ctype = part.get_content_type()
            cdisp = str(part.get("Content-Disposition"))
            if ctype == "text/plain" and "attachment" not in cdisp:
                return part.get_payload(decode=True).decode(
                    part.get_content_charset() or "utf-8", errors="replace"
                )
    else:
        return msg.get_payload(decode=True).decode(
            msg.get_content_charset() or "utf-8", errors="replace"
        )
    return ""

for uid in uids[-10:]:
    typ, msg_data = M.uid("FETCH", uid, "(RFC822)")
    msg = email.message_from_bytes(msg_data[0][1])
    print("UID:", uid.decode(), "| Date:", decode_str(msg["Date"]))
    print("From:", decode_str(msg["From"]))
    print("To:", decode_str(msg["To"]))
    print("Subject:", decode_str(msg["Subject"]))
    print(get_text_body(msg))

M.logout()
```

---

## Scaricamento Allegati e Convenzioni
Per estrarre gli allegati da un messaggio `msg`:
```python
for part in msg.walk():
    filename = part.get_filename()
    if filename:
        filename = decode_str(filename)
        payload = part.get_payload(decode=True)
        # Salvare in allegati/ secondo la convenzione del progetto
```
- Salva gli allegati rilevanti in `allegati/` seguendo la convenzione `yyyymmdd-nomefile-[presentazione|trascrizione|note|altro].est`.
- **Regola formati Office:** per tutti i documenti che arrivano dall'esterno in formati Office (`.pptx`, `.docx`, ecc.), assicurati di avere sempre anche la versione `.pdf` (scaricandola se già disponibile o segnalando/convertendo il file).
