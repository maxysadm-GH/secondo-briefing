# Secondo Feature Tracker
**Last updated:** 2026-04-10 | **Source of truth:** mbacio-shared-knowledge/sync/research/

## What Is This

Tracks every feature of the Secondo system across three states:
- **BUILT** — Shipped in TRON (MBACIO's OpenClaw implementation) or Secondo codebase
- **PITCH** — Validated market need, ready to build
- **PENDING** — Identified gap, needs scoping

## Related Documents

- Feature specs: `mbacio-shared-knowledge/projects/VHC-Ops-Platform-FEATURES.md`
- Repo research: `mbacio-shared-knowledge/sync/research/2026-04-07-second-brain-repo-*.md`
- Second App layers: VHC-Ops-Platform-FEATURES.md → Implementation Layers table
- Investor validation: `mbacio-shared-knowledge/sync/meetings/2026-04-07-second-brain-investor-validation.md`
- garrytan/gbrain research: `mbacio-shared-knowledge/sync/research/gbrain-garrytan.md` (this session)

---

## BUILT (Shipped)

| Feature | Location | Status | Notes |
|---------|----------|--------|-------|
| Multi-agent orchestration | TRON/openclaw-config | Live | ops-agent, coding-agent, research-agent, docs-agent |
| Graduate pipeline | TRON/skills/graduate | Live 2026-04-06 | 9 destination types, 5-phase pipeline |
| Boot sequence | TRON SOUL→IDENTITY→BOOTSTRAP | Live | Human writes, agent reads |
| PARA vault structure | mbacio-shared-knowledge | Live | 00-Inbox through 07-Daily + MOC/ |
| Institutional memory | mbacio-shared-knowledge/institutional-memory | Live | Vendor intel, contracts |
| ShipStation integration | ops-agent | Live | Shipment monitoring, SLA tracking |
| Fishbowl integration | ops-agent | Live | Inventory queries, short positions |
| ClickUp integration | ops-agent | Live | Task creation, project tracking |
| Fathom integration | ops-agent | Live | Meeting transcription, action items |
| Cron-based automation | TRON cron | Live | Morning briefings, EOD summaries |
| Telegram gateway | OpenClaw channels | Live | Group chat with topic routing |
| Skills pipeline | TRON/skills/ | Live | n8n, github-cli, mermaid, security-audit |
| Secondo React Native app | secondo-app | MVP | Voice-first, encrypted vault |
| Supabase backend | secondo-app | Connected | Auth, vault storage |

---

## PITCH (Validated, Ready to Build)

| Feature | Evidence | Market Signal | Priority |
|---------|----------|--------------|----------|
| Voice input/output (VoiceMode) | 3 repos added 2026-04, Greg Isenberg masterclass | 1.7M views on second brain content | HIGH |
| Cadence staleness scoring | modular-context repo, living-wiki | New pattern from 4 repos in 7 days | HIGH |
| /context command | living-wiki Three Loops | Query compounds knowledge | HIGH |
| Entity propagation pipeline | garrytan/gbrain | 3,600+ stars, MIT licensed | HIGH |
| pgvector hybrid search (Supabase) | garrytan/gbrain | pgvector + keyword + RRF fusion | HIGH |
| Brain-first agent loop | garrytan/gbrain | Check brain before web search | HIGH |
| /pattern command | living-wiki maintenance loop | Anomaly surfacing | MEDIUM |
| Dream cycle (nightly enrichment) | garrytan/gbrain | Agent runs while human sleeps | MEDIUM |
| Karpathy LLM Wiki ingest | NicholasSpisak, zhiwehu repos | 4 repos based on this pattern | MEDIUM |
| PARA auto-classification | zhiwehu/second-brain | 7 content type handlers | MEDIUM |
| /vault-audit enhancement | modular-context cadence | Staleness scoring | MEDIUM |

---

## PENDING (Identified Gaps)

| Feature | Maps To | Blocker |
|---------|---------|---------|
| Secondo mobile app (voice-first) | Secondo codebase | Engineering |
| Encrypted vault | Secondo codebase | Security review |
| Outbound call agent | Secondo codebase | Twilio integration |
| Second App Layers 5-8 | VHC-Ops-Platform-FEATURES.md | Not started |
| /delegate command | VHC-Ops-Platform-FEATURES.md | Not scoped |
| Business/Teams multi-tenant | VHC-Ops-Platform-FEATURES.md | Not scoped |
| /graduate → ClickUp auto-task | TRON/skills/graduate | Needs ClickUp MCP |
| Living Wiki compile loop | living-wiki Loop 2 | Not built |
| garrytan/gbrain entity propagation | This session | License: MIT, use freely |

---

## Quick Build Order (Based on Market Signals)

1. **/graduate** → Already built ✓
2. **/vault-audit + cadence scoring** → Add staleness to existing /vault-audit
3. **/context command** → Callable mid-session state injection
4. **garrytan/gbrain entity propagation** → Reference MIT license, build into ops-agent
5. **pgvector hybrid search** → Supabase already connected, enable pgvector
6. **Brain-first agent loop** → Check brain before web search
7. **VoiceMode** → Install on TRON
8. **Dream cycle** → Nightly enrichment cron

---

## garrytan/gbrain Reference (MIT License — Free to Use)

**Repo:** github.com/garrytan/gbrain
**Stars:** 3,600+ | **Created:** 2026-04 | **License:** MIT

### Key Patterns to Reference

| Pattern | What it does | How to use |
|---------|-------------|-------------|
| Entity propagation | Ingest meeting → update all person/company pages automatically | Free to reference |
| Append-only timelines | Evidence never deleted, compiled truth on top | Free to reference |
| Dream cycle | Agent runs while human sleeps, enriches knowledge graph | Free to reference |
| Brain-first loop | Check brain before web search | Free to reference |
| pgvector hybrid search | Vector + keyword + RRF fusion | Free to reference |

### garrytan/gbrain Architecture (Reference Only)

```
Brain Repo (git) → pgvector + Supabase → AI Agent
                    ↑
              Entity propagation
              (people/company pages auto-updated on ingest)
              Append-only timelines (evidence trail)
              Compiled truth (latest understanding on top)
              Dream cycle (nightly enrichment while human sleeps)
```

### Supabase Already Connected

- Secondo app already has Supabase connected ✓
- pgvector extension needed: `CREATE EXTENSION IF NOT EXISTS vector;`
- Reference garrytan/gbrain MIT license patterns freely

---

## GitHub Repos (Last 30 Days)

| Repo | Stars | Created | Key Pattern |
|------|-------|---------|-------------|
| My-Brain-Is-Full-Crew | 1,996 | 2026-03-21 | 8-agent crew, 13 skills |
| second-brain-starter | 275 | 2026-03-29 | PRD generator |
| ai-career-planner-skill | 94 | 2026-04-02 | Career-focused skill |
| zhiwehu/second-brain | 16 | 2026-04-06 | OpenClaw-native, 7 content types |
| modular-context | 10 | 2026-04-05 | Cadence staleness scoring |
| living-wiki | 3 | 2026-04-04 | Three-loop self-improving |
| NicholasSpisak/second-brain | 3 | 2026-04-06 | Karpathy LLM Wiki pattern |
| **garrytan/gbrain** | **3,600+** | **2026-04** | **Entity propagation, pgvector, dream cycle** |

Full research: `mbacio-shared-knowledge/sync/research/2026-04-07-second-brain-repo-*.md`

---

## NaviLum Content Agent (HIGH PRIORITY — garrytan/gbrain patterns apply)

### What NaviLum Is
AI content agent for MBACIO marketing. Ingested from `~/Projects/easy-it-growth/`. Lives in `navilum-content/` git-synced workspace.

### garrytan/gbrain Patterns That Apply to NaviLum

| garrytan Pattern | NaviLum Application |
|---|---|
| Entity propagation | Every piece of content → update brand voice, client brief, campaign record |
| Append-only timelines | Content decisions traceable to proof/narrative |
| Brain-first loop | NaviLum checks content memory before generating |
| Dream cycle | Overnight content performance analysis → content memory update |
| pgvector search | "Find similar successful content about [topic/brand/vertical] |
| Compiled truth on top | Brand voice document — updated on ingest, evidence trail preserved |

### NaviLum Current State

- Content corpus: `~/easy-it-growth/marketing/` (blog posts, social copy, ad creatives)
- Brand truth: `~/mbacio-shared-knowledge/marketing/brand-voice.md`
- Campaign briefs: `~/easy-it-growth/marketing/` (campaign briefs, ad creatives)
- Execution: Marketing agent via Telegram bot
- Output: Telegram → Max reviews → publish

### garrytan Additions for NaviLum

| New Feature | Description |
|---|---|
| `/navilum-brain-first` | Check content corpus before generating |
| `/navilum-entity-propagate` | Update brand/client/campaign pages on ingest |
| `/navilum-dream` | Nightly content performance → content memory update |
| pgvector search | "What's worked on [topic]?" across content corpus |
| Append-only content log | Content decisions traceable to evidence |

### Next Step
Add NaviLum section to `mbacio-shared-knowledge/projects/INDEX.md` and create `navilum-content/` workspace. Route via `/navilum` command.
