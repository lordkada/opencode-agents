# Linee Guida Repository `opencode-agents`

Questo repository raccoglie skill, istruzioni e template condivisi tra più progetti OpenCode.

## Convenzioni di Manutenzione del Repository
- **Percorsi relativi**: utilizza sempre percorsi relativi alla root del progetto in tutta la documentazione e nelle skill.
- **Sicurezza e Neutralità**: non inserire mai token, cookie, password, ID sensibili reali o riferimenti a specifiche organizzazioni nei file delle skill o negli esempi. Usa esclusivamente placeholder generici (es. `xoxc-inserisci-qui...`, `nome.cognome@example.com`, `mio-team.fellow.app`) e assicurati che `.env.template` sia sempre allineato alle variabili richieste dalle skill.
- **Struttura delle Skill (`.agents/skills/<categoria>/<skill-id>/SKILL.md`)**:
  - Le skill sono organizzate in sottocartelle per tipologia (es. `integrations/`, `product-management/`, `presentations/`).
  - Il nome della cartella foglia (`<skill-id>`) determina l'ID della skill (usa sempre `kebab-case`).
  - Ogni `SKILL.md` deve avere un frontmatter YAML con `name` e `description` chiari.
  - Ogni categoria ha il proprio catalogo HTTP `.agents/skills/<categoria>/index.json`. Ogni volta che aggiungi, rinomini o modifichi una skill (o i suoi file in `references/` / `scripts/`), aggiorna l'`index.json` della relativa categoria incrementando il campo `"version"` della skill modificata.
