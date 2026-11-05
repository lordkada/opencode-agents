---
name: Connettore Atlassian MCP (Jira, Confluence, Bitbucket, Loom)
description: Configura e utilizza il server MCP remoto ufficiale di Atlassian v2 (https://mcp.atlassian.com/v2/mcp) per cercare, leggere, creare e aggiornare issue Jira, pagine Confluence, richieste JSM, repository Bitbucket, video Loom e Teamwork Graph. Usa questa skill quando devi configurare Atlassian MCP in OpenCode o interagire con i prodotti Atlassian.
---

# Skill: Server MCP Atlassian v2 (Jira, Confluence, JSM, Bitbucket, Loom, Rovo)

Istruzioni per configurare, autenticare e utilizzare il **server MCP remoto ufficiale di Atlassian (v2)** in OpenCode secondo la [guida ufficiale Atlassian](https://support.atlassian.com/atlassian-ai-gateway/docs/get-started-with-the-atlassian-remote-mcp-server/).

Per conoscere le **chiavi di progetto Jira (`PROJECT_KEY`), gli Space Confluence, le Board o i filtri** specifici del progetto corrente, consulta il file `AGENTS.md` nella root del progetto.

---

## 1. Configurazione del Server MCP in OpenCode V2

L'endpoint ufficiale del server MCP remoto di Atlassian (v2, trasporto Streamable HTTP) è:
```text
https://mcp.atlassian.com/v2/mcp
```

### Metodo rapido da CLI OpenCode
Per aggiungere il server a livello globale (disponibile in tutti i progetti) preservando il resto della configurazione:
```bash
opencode mcp add atlassian --global --url https://mcp.atlassian.com/v2/mcp
opencode mcp list
```
*(Ometti `--global` se vuoi salvarlo solo nel file `opencode.jsonc` del progetto corrente).*

### Configurazione dichiarativa in `opencode.jsonc`
In OpenCode V2 i server MCP si configurano sotto `mcp.servers` (nel file di progetto `opencode.jsonc` oppure in quello globale `~/.config/opencode/opencode.jsonc`, che in `opencode-as-a-container` è mappato sul Mac in `~/.local/share/opencode-container/opencode-data/.config/opencode/opencode.jsonc`):

```jsonc
{
  "$schema": "https://opencode.ai/config.json",
  "mcp": {
    "servers": {
      "atlassian": {
        "type": "remote",
        "url": "https://mcp.atlassian.com/v2/mcp"
      }
    }
  }
}
```

*(Nota: se utilizzi un gateway MCP o necessiti dell'elenco piatto completo di tutti i tool invece del meccanismo `discover` + `execute*`, puoi usare l'URL `https://mcp.atlassian.com/v2/mcp?tools=all`).*

---

## 2. Autenticazione (OAuth 2.1 oppure API Token)

### Opzione A: OAuth 2.1 (Predefinita e Consigliata)
OpenCode abilita automaticamente **OAuth 2.1 con PKCE** per i server `"type": "remote"`.
1. Una volta configurato il server `atlassian`, se `opencode mcp list` riporta `needs authentication`, chiedi all'utente di eseguire **`/mcps`** nell'interfaccia di OpenCode, selezionare **`atlassian`** e completare il login e il consenso nel browser.
2. **Come completare OAuth 2.1 dentro il Container Docker (`opencode-as-a-container`):**
   - Quando OpenCode gira in un container isolato, il browser sul Mac dell'utente al termine del consenso Atlassian viene reindirizzato a un URL locale del tipo `http://127.0.0.1:<porta>/callback?code=...&state=...` e mostra *"Impossibile raggiungere il sito"*.
   - **Soluzione immediata:** OpenCode V2 supporta l'inserimento manuale dell'URL di callback! Chiedi all'utente di **copiare l'intero URL `http://127.0.0.1:...` dalla barra degli indirizzi del browser** e **incollarlo direttamente nella finestra `/mcps` di OpenCode** per completare l'autenticazione OAuth senza bisogno di esporre porte dal container.
3. **Importante per l'agente:** non eseguire mai `opencode mcp auth atlassian` tramite il tool `shell` in background, poiché avvia un flusso interattivo il cui link di autorizzazione resterebbe nascosto; invita sempre l'utente a usare `/mcps` nella TUI.
4. Le credenziali OAuth sono salvate da OpenCode esternamente ai file di configurazione del progetto e rispettano i permessi utente e il `cloudId` autorizzato.

### Opzione B: Autenticazione tramite API Token (Alternativa per Container Docker / Ambienti Headless)
Se il browser locale non può completare il redirect OAuth verso l'ambiente di esecuzione (es. container Docker isolato senza porte di callback esposte) e l'amministratore dell'organizzazione Atlassian ha abilitato l'autenticazione via API Token:

1. **Personal API Token (Basic Auth):**
   - L'utente crea un Personal API Token con gli scope desiderati su:
     `https://id.atlassian.com/manage-profile/security/api-tokens?autofillToken&expiryDays=max&appId=mcp-v2&selectedScopes=all`
   - Codifica in Base64 la stringa `<email>:<api_token>`:
     ```bash
     echo -n "nome.cognome@example.com:IL_TUO_API_TOKEN" | base64
     ```
   - Salva il valore risultante nella variabile d'ambiente `ATLASSIAN_MCP_BASIC_AUTH` (es. in `.env` / `~/.config/opencode/.env`) e configura `opencode.jsonc` disabilitando OAuth:
     ```jsonc
     {
       "$schema": "https://opencode.ai/config.json",
       "mcp": {
         "servers": {
           "atlassian": {
             "type": "remote",
             "url": "https://mcp.atlassian.com/v2/mcp",
             "oauth": false,
             "headers": {
               "Authorization": "Basic {env:ATLASSIAN_MCP_BASIC_AUTH}"
             }
           }
         }
       }
     }
     ```
2. **Service Account API Key (Bearer Token):**
   - Con una chiave API di un service account Atlassian salvata in `ATLASSIAN_MCP_BEARER_TOKEN`:
     ```jsonc
     "headers": {
       "Authorization": "Bearer {env:ATLASSIAN_MCP_BEARER_TOKEN}"
     }
     ```

---

## 3. Architettura dei Tool in Atlassian MCP v2

Per ridurre il consumo di contesto, il server MCP di Atlassian v2 espone direttamente un set compatto di **Primary Tools** e rende disponibili tutti gli altri tool on-demand tramite **`discover`** ed **`execute*`**.
*(In OpenCode con Code Mode attivo di default, i tool del server `atlassian` sono invocabili sotto il namespace `tools.atlassian.*` o `atlassian_*`).*

### Passo 1 obbligatorio: Recuperare il `cloudId`
Quasi tutte le chiamate ai tool Atlassian richiedono il parametro `cloudId` del sito Atlassian Cloud di destinazione:
- Chiama sempre per primo **`getAccessibleAtlassianResources`** per ottenere l'elenco dei siti Atlassian accessibili e i relativi `cloudId` (specialmente quando si usa l'autenticazione via API Token, che non è vincolata a un singolo `cloudId`).
- Usa **`atlassianUserInfo`** quando ti serve conoscere l'account ID o il profilo dell'utente corrente.

### Passo 2: Uso dei Primary Tools (Diretti)
I seguenti tool primari sono esposti direttamente senza passare da `discover`:
- **Jira:**
  - `getJiraIssue`: legge i dettagli di un work item Jira tramite ID o chiave (es. `PROJ-123`).
  - `searchJiraIssuesUsingJql`: cerca work item Jira tramite query JQL.
  - `createJiraIssue`: crea un nuovo work item Jira.
  - `editJiraIssue`: modifica i campi di un work item esistente.
  - `transitionJiraIssue`: cambia lo stato di un work item o lo assegna a sprint/backlog.
  - `addOrEditJiraIssueComment`: aggiunge o modifica un commento su un work item.
- **Confluence:**
  - `getConfluenceContent`: legge pagine, blog post, live doc, whiteboard, database o commenti Confluence.
  - `searchConfluence`: cerca contenuti su Confluence tramite query CQL.
  - `createConfluenceContent`: crea pagine, live doc, whiteboard, cartelle o altri contenuti Confluence.
  - `updateConfluenceContent`: aggiorna contenuti Confluence (sostituzione completa o modifiche granulari).
- **Loom:**
  - `getLoomObjectsContent`: recupera metadati, trascrizioni, riassunti e action item di video e meeting registrati su Loom.
- **Teamwork Graph & Rovo Search:**
  - `getTeamworkGraphContext` / `getTeamworkGraphObject`: attraversa le relazioni tra entità (issue Jira, pagine Confluence, PR, persone, goal, progetti).
  - `search`: ricerca semantica unificata in linguaggio naturale tramite Rovo.

### Passo 3: Uso dei Deferred Tools tramite `discover` + `execute*`
Per tutte le operazioni avanzate non presenti tra i Primary Tools (es. gestione Sprint e Board Jira, versioni/release, worklog, allegati, permessi e versioni Confluence, Jira Service Management `JSM`, repository/PR/pipeline `Bitbucket`, `Goals`, `Projects`, `Teams`, `Focus`, `Assets`, `Talent`, `Capacity Planning`, `Jira Align`):
1. Chiama **`discover`** passando una descrizione in linguaggio naturale dell'operazione che ti serve: otterrai i nomi dei tool pertinenti e i relativi `toolSchema`.
2. Prima di creare o formattare contenuti ricchi su Confluence o whiteboard, puoi recuperare la guida di formattazione invocando `getContentFormatGuide` tramite **`executeRead`**.
3. Esegui il tool individuato tramite il canale appropriato in base all'impatto dell'operazione:
   - **`executeRead`**: per tutte le operazioni di sola lettura, elencazione o ispezione (es. `listJiraBoards`, `getJiraBoardSprintData`, `listConfluenceSpaces`, `listBitbucketRepoPullRequests`).
   - **`executeWrite`**: per operazioni di creazione o modifica (es. `manageJiraSprint`, `createJiraIssueLink`, `createConfluenceComment`, `createBitbucketRepoPullRequest`).
   - **`executeDestructive`**: per operazioni distruttive o irreversibili (es. `deleteJiraIssue`, `deleteJiraComment`, `replaceConfluenceContentPermissions`).

---

## 4. Regole di Sicurezza e Consumo Crediti Rovo

- **Sola lettura sempre consentita:** la ricerca e la lettura di issue Jira, pagine Confluence, PR Bitbucket, trascrizioni Loom e metadati (`get*`, `list*`, `search*`, `executeRead`) sono sempre consentite senza necessità di conferma preventiva.
- **Conferma obbligatoria per scrittura e modifiche:** prima di eseguire qualsiasi operazione che crea, modifica, transiziona, commenta o elimina risorse (`createJiraIssue`, `editJiraIssue`, `transitionJiraIssue`, `addOrEditJiraIssueComment`, `createConfluenceContent`, `updateConfluenceContent`, `executeWrite`, `executeDestructive`), mostra sempre all'utente un riepilogo chiaro (progetto/space, chiave o titolo, campi/contenuto) e **attendi conferma esplicita**, salvo istruzione diretta già confermata dall'utente.
- **Consumo crediti Rovo:** le chiamate ai tool arricchiti del Teamwork Graph e di Rovo (`getTeamworkGraphContext`, `search`) consumano crediti Rovo dal pool condiviso dell'organizzazione. Quando conosci già il progetto, lo space o i criteri strutturati, **preferisci le ricerche mirate JQL (`searchJiraIssuesUsingJql`), CQL (`searchConfluence`) o il recupero diretto per ID/chiave (`getJiraIssue`, `getConfluenceContent`)**, riservando `search` e `getTeamworkGraphContext` alle ricerche cross-app o semantiche.
- **Protezione delle credenziali:** non stampare né committare mai in chiaro token API Atlassian o stringhe Base64 di autenticazione eventualmente presenti in `.env`.
