---
name: personal-os
description: Use this skill when the user wants to set up, configure, extend, or understand their Personal OS — the AI-powered operating system built on Claude Code. Covers all components: MCP servers, agents, skills, memory, rules, and Gemma routing. Invoke when the user says "personal os", "configura il sistema", "aggiungi un agente", "crea una skill", "setup claude code", or asks how their AI system works.
argument-hint: "[setup | mcp | agent | skill | memory | status]"
---

# Personal OS — Guida Operativa per Claude Code

## Cos'è il Personal OS

Il Personal OS è un sistema AI operativo personalizzato costruito su Claude Code. Non è un chatbot: è un **sistema esecutivo** che può agire su tool reali (email, ads, calendar, CRM), ricordare il contesto tra sessioni, e invocare specialisti AI in parallelo.

**Architettura a 4 livelli:**

```
┌──────────────────────────────────────┐
│  PERSONALITÀ — Agenti · Skills · Rules│
├──────────────────────────────────────┤
│  CERVELLO  — Claude Code             │
├──────────────────────────────────────┤
│  MANI      — MCP Server              │
├──────────────────────────────────────┤
│  MEMORIA   — MEMORY.md + file .md    │
└──────────────────────────────────────┘
```

---

## Come Usare Questa Skill

Se l'utente invoca `/personal-os` senza argomenti, chiedi cosa vuole fare:

```
Cosa vuoi configurare nel tuo Personal OS?

1. **setup** — Configura da zero (struttura cartelle, CLAUDE.md, memoria)
2. **mcp** — Aggiungi o verifica MCP server
3. **agent** — Crea o installa un agente specializzato
4. **skill** — Crea o installa una skill
5. **memory** — Configura o aggiorna la memoria cross-sessione
6. **status** — Verifica lo stato attuale del sistema
```

Se l'utente passa un argomento direttamente (es. `/personal-os mcp`), vai direttamente alla sezione corrispondente.

---

## SETUP — Configurazione da Zero

### Step 1: Struttura cartelle

```bash
mkdir -p ~/.claude/agents
mkdir -p ~/.claude/skills
mkdir -p ~/.claude/rules/common
mkdir -p ~/.claude/commands
mkdir -p ~/.claude/hooks
```

### Step 2: CLAUDE.md globale

Crea `~/.claude/CLAUDE.md` con le istruzioni di base:

```markdown
# Istruzioni Globali

## Lingua e Output
- Rispondi in italiano
- Usa tabelle markdown per dati strutturati
- Salva output in cartelle datate: YYYY-MM-DD-nome/

## Routing Gemma/Claude
Usa Gemma 3 via Ollama per: bozze email, riassunti, traduzioni, varianti copy, categorizzazione.
Usa Claude per: MCP, coding, analisi strategica, orchestrazione workflow.

### Chiamata Gemma (automatica)
Verifica Ollama attivo:
`curl -s http://localhost:11434/api/tags > /dev/null 2>&1 || echo "OFFLINE"`

Se attivo, delega via bash:
`ollama run gemma3:12b "PROMPT" 2>/dev/null`
```

### Step 3: Installa Gemma (opzionale, risparmia token)

```bash
# Da ollama.ai — installa Ollama prima
ollama pull gemma3:12b
ollama run gemma3:12b "ciao"  # test
```

### Step 4: Memoria iniziale

```bash
# Crea la struttura memoria per il progetto corrente
mkdir -p .claude/memory
touch .claude/memory/MEMORY.md
```

Poi crea `.claude/memory/user_profile.md` con il profilo dell'utente (vedi sezione MEMORY).

---

## MCP — Model Context Protocol

### Cosa sono i MCP Server

I MCP server connettono Claude a tool reali. Ogni server espone tool invocabili:
- `mcp__meta-ads__get_insights` → dati campagna Meta in tempo reale
- `mcp__gmail__search_emails` → cerca nella tua casella email
- `mcp__google-calendar__create_event` → crea un evento nel calendario

### MCP via claude.ai (più facile, cloud)

1. Vai su **claude.ai → Settings → Integrations**
2. Connetti: Gmail, Google Calendar, Google Drive, Notion, Asana, Canva
3. I tool appaiono automaticamente in ogni sessione

### MCP locali (più potente, richiede setup)

File di configurazione: `~/.claude/mcp_settings.json`

```json
{
  "mcpServers": {
    "meta-ads": {
      "command": "node",
      "args": ["/path/to/meta-ads-manager/index.js"],
      "env": {
        "META_ACCESS_TOKEN": "EAAxxxxxx"
      }
    },
    "google-ads": {
      "command": "python",
      "args": ["/path/to/google-ads-manager/server.py"],
      "env": {
        "GOOGLE_ADS_DEVELOPER_TOKEN": "xxxxx",
        "GOOGLE_ADS_CLIENT_ID": "xxxxx",
        "GOOGLE_ADS_CLIENT_SECRET": "xxxxx",
        "GOOGLE_ADS_REFRESH_TOKEN": "xxxxx"
      }
    },
    "gmail": {
      "command": "node",
      "args": ["/path/to/server-gmail/dist/index.js"]
    },
    "google-calendar": {
      "command": "node",
      "args": ["/path/to/server-calendar/dist/index.js"],
      "env": {
        "GOOGLE_CLIENT_ID": "xxxxx",
        "GOOGLE_CLIENT_SECRET": "xxxxx",
        "GOOGLE_REFRESH_TOKEN": "xxxxx"
      }
    }
  }
}
```

### Verifica MCP attivi

Quando l'utente chiede di verificare lo stato MCP, esegui un tool di test per ciascun server e mostra una tabella semaforo:

| Server | Status | Tool Testato |
|--------|--------|--------------|
| meta-ads | 🟢 OK | `list_ad_accounts` |
| google-ads | 🟢 OK | `health_check` |
| gmail | 🟡 DA VERIFICARE | connessione assente |
| google-calendar | 🔴 OFFLINE | errore connessione |

### MCP Server da installare per dominio

**Marketer B2B:**
- Meta Ads MCP (campagne, insight, audience)
- Google Ads MCP (search, display, pmax)
- Gmail MCP (email, follow-up, report ai clienti)
- Google Calendar (scheduling, promemoria)
- Notion (CRM leggero, note cliente)

**Sales / RevOps:**
- Gmail + Calendar (gestione pipeline via email)
- Notion o Asana (task e deal tracking)
- Zapier (trigger automazioni HubSpot/Salesforce)

**Content / SEO:**
- Bright Data (scraping SERP, competitor analysis)
- Canva (generazione visual)
- Google Drive (accesso ai documenti del team)

---

## AGENT — Agenti Specializzati

### Struttura di un agente

File: `.claude/agents/nome-agente.md`

```markdown
---
name: nome-agente
description: Quando usarlo. Triggers espliciti: "analizza pipeline", "review call", "ottimizza ads"
---

# Nome Agente

## Ruolo
Sei un esperto di [dominio]. Il tuo obiettivo è [obiettivo].

## Metodologia
1. [Step 1]
2. [Step 2]
3. [Step 3]

## Output
Rispondi SEMPRE con:
- Sintesi in 3 bullet
- Tabella dati strutturati
- 3 azioni prioritarie con impatto stimato (Alto/Medio/Basso)
```

### Come creare un nuovo agente

Quando l'utente vuole creare un agente, raccogli queste informazioni:

1. **Dominio**: In quale area opera? (paid media, sales, contenuto, ops...)
2. **Trigger**: Quando deve essere invocato? Esempi specifici
3. **Metodologia**: Quali framework usa? (MEDDPICC, AIDA, RACE...)
4. **Output**: Qual è il formato di risposta atteso?
5. **Tool MCP**: Ha bisogno di accedere a dati esterni?

Poi genera il file agente e salvalo in `.claude/agents/`.

### Agenti per dominio — template rapido

**Paid Media Auditor:**
```markdown
---
name: paid-media-auditor
description: Usa quando l'utente chiede di analizzare, auditare o ottimizzare campagne paid (Meta, Google, LinkedIn). Trigger: "come vanno le campagne", "audit ads", "analizza performance".
---
Sei un esperto paid media con 10 anni di esperienza su Meta Ads e Google Ads.
Metodologia: analisi in 5 aree (struttura, targeting, creative, budget, tracking).
Output: tabella semaforo KPI + top 3 problemi + top 3 opportunità + action plan.
```

**Sales Pipeline Analyst:**
```markdown
---
name: sales-pipeline-analyst
description: Usa per analisi pipeline, forecast, deal velocity. Trigger: "come va il pipeline", "previsione mese", "analisi deal".
---
Sei un revenue operations expert specializzato in pipeline health e forecast accuracy.
Metodologia: analisi per stage (MQL→SQL→Opportunity→Closed Won), conversion rates, velocity.
Output: pipeline health score + bottleneck principale + azioni correttive.
```

### Invocare un agente

Claude Code invoca automaticamente l'agente corretto in base alla descrizione. Puoi anche invocarlo esplicitamente:

```
Usa il paid-media-auditor per analizzare le campagne Meta degli ultimi 30 giorni
```

---

## SKILL — Workflow Riutilizzabili

### Differenza agente vs skill

| | Agente | Skill |
|--|--------|-------|
| Cos'è | Personalità AI specializzata | Procedura step-by-step |
| Come si invoca | Automaticamente o con `Agent()` | `/nome-skill` |
| Uso tipico | Analisi, consulenza, review | Workflow, generazione documenti |

### Struttura di una skill

File: `.claude/skills/nome-skill/SKILL.md`

```markdown
---
name: nome-skill
description: Quando usare questa skill. Triggers: "genera report", "crea deck", "analizza competitor"
argument-hint: "[parametro opzionale]"
---

# Nome Skill

## Istruzioni

Quando questa skill viene invocata:

### Step 1: Raccolta input
- Se [parametro] non è fornito, chiedi: "..."
- Se i dati servono da MCP, chiama [tool] prima

### Step 2: Elaborazione
[logica specifica]

### Step 3: Output
Produci sempre:
- [file / risposta strutturata]
- Salva in: `reports/YYYY-MM-DD-nome/`
```

### Skill più utili da installare

| Skill | Comando | Cosa fa |
|-------|---------|---------|
| `ads:health-check` | `/ads:health-check` | Audit KPI campagne, 7 giorni, semaforo |
| `ads:optimize` | `/ads:optimize` | Problemi + opportunità + action plan |
| `pptx` | `/pptx` | Genera PowerPoint professionale |
| `pdf` | `/pdf` | Legge, estrae, combina PDF |
| `ads:spy` | `/ads:spy competitor=URL` | Intelligence competitor ads + organico |

---

## MEMORY — Memoria Cross-Sessione

### Struttura

```
.claude/memory/
├── MEMORY.md              ← indice (max 200 righe, caricato auto)
├── user_profile.md        ← chi sei, ruolo, expertise
├── feedback_stile.md      ← come preferisci lavorare
├── project_[nome].md      ← stato progetti attivi
└── reference_[nome].md    ← dove trovare dati esterni
```

### MEMORY.md — formato

```markdown
# Memory Index

- [Profilo Utente](user_profile.md) — [sintesi 1 riga]
- [Stile di Lavoro](feedback_stile.md) — [sintesi 1 riga]
- [Progetto X](project_x.md) — [sintesi 1 riga]
```

### Template user_profile.md

```markdown
---
name: User Profile
description: Ruolo, background, expertise, contesto professionale
type: user
---

# Chi Sono

- **Ruolo**: [es. CMO, Head of Growth, Marketing Manager]
- **Azienda**: [nome] — [settore B2B/B2C]
- **Focus**: [es. paid media, pipeline sales, contenuto]
- **Tool principali**: [es. Meta Ads, HubSpot, Notion, Google Ads]
- **Lingua preferita**: italiano
- **Anni esperienza**: [X]
```

### Template feedback_stile.md

```markdown
---
name: Stile di Lavoro
description: Come preferisce lavorare, output attesi, cosa evitare
type: feedback
---

# Preferenze

- Output sempre in italiano
- Tabelle markdown per dati strutturati
- Risposte concise — no riassunti inutili
- Salva file in cartelle datate YYYY-MM-DD-nome/
- Conferma prima di azioni irreversibili (email inviate, campagne attivate)
```

### Aggiornare la memoria durante la sessione

Quando impari qualcosa di nuovo sull'utente o sui progetti:
1. Controlla se esiste già un file memoria pertinente
2. Se sì, aggiorna il file esistente
3. Se no, crea un nuovo file e aggiungi il puntatore a MEMORY.md
4. Non duplicare memoria già presente

---

## STATUS — Verifica Sistema

Quando l'utente chiede lo stato del sistema, esegui questa checklist:

### Verifica MCP
```bash
# Testa Ollama (Gemma)
curl -s http://localhost:11434/api/tags > /dev/null 2>&1 && echo "GEMMA: online" || echo "GEMMA: offline"
```

Poi testa i tool MCP con chiamate leggere (list/health) e mostra la tabella semaforo.

### Inventario componenti

Mostra:
- Numero agenti installati in `.claude/agents/`
- Numero skills in `.claude/skills/`
- MCP server attivi
- File memoria presenti in `.claude/memory/`

### Output status

```
## Stato Personal OS

| Componente | Status | Dettaglio |
|------------|--------|-----------|
| Claude Code | 🟢 | Versione X.X |
| Gemma 3 (Ollama) | 🟢 / 🔴 | online / offline |
| MCP Meta Ads | 🟢 / 🔴 | X tool disponibili |
| MCP Gmail | 🟢 / 🔴 | — |
| Agenti installati | — | X agenti |
| Skills installate | — | X skills |
| Memoria | — | X file, ultimo aggiornamento |
```

---

## Regole di Comportamento (Rules)

Le rules sono istruzioni sempre attive. Si trovano in `~/.claude/rules/`.

### Struttura consigliata

```
~/.claude/rules/
└── common/
    ├── output-style.md     ← lingua, formato, dove salvare
    ├── routing.md          ← quando usare Gemma vs Claude
    ├── confirmations.md    ← cosa richiedere conferma prima di fare
    └── security.md         ← no secrets nel codice, validazione input
```

### Regola conferme obbligatorie

```markdown
# Conferme Obbligatorie

Prima di eseguire, chiedi sempre conferma per:
- Inviare email (`mcp__gmail__send_email`)
- Attivare campagne (`resume_campaign`)
- Eliminare qualsiasi cosa (`delete_*`)
- Push git su main/master
- Pagamenti o azioni con effetti monetari
```

---

## Esempi di Utilizzo Rapido

```
# Audit campagne Meta Ads
/ads:health-check

# Analizza un competitor
/ads:spy competitor=nomesito.it

# Genera una presentazione
/pptx crea una deck per il cliente su risultati Q1

# Configura un nuovo agente
/personal-os agent

# Aggiungi un MCP server
/personal-os mcp

# Verifica che tutto funzioni
/personal-os status
```

---

## Limitazioni da Ricordare

| Limite | Soluzione |
|--------|-----------|
| Claude sbaglia su task ambigui | Verifica sempre output critici |
| MCP rotti se le API cambiano | Aggiorna i server MCP periodicamente |
| Memoria non infinita | Mantieni MEMORY.md sotto 200 righe |
| Gemma offline → usa Claude | Fallback automatico, nessun errore visibile |
| Setup richiede 2-4 ore | Investi una volta, ROI permanente |
