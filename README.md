# Personal OS Skill

Skill completa per Claude Code. Configura, estendi, e gestisci il tuo Personal OS — sistema AI operativo personalizzato.

## Cos'è

Personal OS è un'architettura a 4 livelli:

```
Personalità (Agenti + Skills + Rules)
         ↓
Claude Code (Cervello)
         ↓
MCP Servers (Mani — Gmail, Ads, Calendar...)
         ↓
Memoria cross-sessione
```

## Quick Start

### 1. Setup da zero
```bash
/personal-os setup
```

### 2. Aggiungi MCP
```bash
/personal-os mcp
```

### 3. Crea agente specializzato
```bash
/personal-os agent
```

### 4. Verifica sistema
```bash
/personal-os status
```

## Componenti

- **SETUP** — Struttura cartelle, CLAUDE.md, memoria iniziale
- **MCP** — Connessioni a Gmail, Google Ads, Meta Ads, Calendar, Notion, ecc.
- **AGENT** — Crea agenti specializzati (auditor, analyst, coach, strategist...)
- **SKILL** — Workflow riutilizzabili (/comando)
- **MEMORY** — Memoria persistente cross-sessione
- **STATUS** — Verifica stato sistema

## Struttura consigliata

```
~/.claude/
├── agents/             # Personalità specializzate
├── skills/             # Workflow riutilizzabili
├── rules/common/       # Istruzioni globali
├── memory/             # Memoria cross-sessione
├── commands/           # Comandi slash rapidi
├── hooks/              # Automazioni trigger
└── CLAUDE.md          # Config globale
```

## Requisiti

- Claude Code (CLI, desktop, o extension)
- GitHub account (per MCP cloud)
- Optional: Ollama + Gemma 3 (risparmia token su task semplici)

## MCP Servers da installare

### Essenziale
- Meta Ads (campagne, insight, audience)
- Google Ads (search, display, pmax)
- Gmail (email, follow-up)
- Google Calendar (scheduling)

### Opzionale per ruolo
- **Content/SEO**: Canva, Bright Data
- **Sales**: Notion, Asana, Zapier
- **Growth**: Clearbit, Segment

## Comandi disponibili

| Comando | Effetto |
|---------|---------|
| `/personal-os setup` | Configura da zero |
| `/personal-os mcp` | Aggiungi/verifica MCP server |
| `/personal-os agent` | Crea agente specializzato |
| `/personal-os skill` | Crea skill (workflow) |
| `/personal-os memory` | Configura memoria |
| `/personal-os status` | Verifica stato sistema |

## Esempi d'uso

```bash
# Audit campagne Meta Ads (skill ads:health-check)
/ads:health-check

# Analizza competitor
/ads:spy competitor=nomesite.it

# Crea presentazione
/pptx

# Verifica MCP attivi
/personal-os status
```

## Limitazioni

| Limite | Soluzione |
|--------|-----------|
| Setup richiede 2-4 ore | Fai una volta, ROI permanente |
| MCP rotti se API cambiano | Aggiorna server periodicamente |
| Memoria non infinita | Mantieni MEMORY.md <200 righe |
| Gemma offline | Fallback automatico a Claude |

## Docs

Leggi **SKILL.md** per la guida operativa completa (setup, MCP, agent, skill, memory, status).

---

**Autore**: Massimiliano Pavesio  
**Licenza**: MIT  
**Repo**: https://github.com/maxpavesio/personal-os-skill
