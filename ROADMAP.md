# Secondo — Timeline, Roadmap & Deliverables

> Written 2026-04-20 for the Andy briefing on 2026-04-21. Rolls up `.planning/STATE.md`, `.planning/ROADMAP.md`, `.planning/APRIL-28-DEMO-PUNCH-LIST.md`, and the Welvey Labs legal calendar into one page.

## Snapshot

- **Overall progress:** ~80%. Phases 00–05 complete; Phase 06 (voice + AI call) ~70%; Phase 10 (engagement/paywall) plan drafted.
- **iOS production build:** shipped 2026-04-17 (EAS `572b57f0`, Apple Team `UUH73UUQ7D`, ASC `6762532059` — "Secondo AI").
- **Patent demo:** April 28, 2026 — T-7 days.
- **Provisional Patent #1 filing:** April 30, 2026.
- **Provisional Patent #2 filing:** June 30, 2026.
- **Round target:** $1M SAFE (part of $1.5–3M seed).

## Deliverables by date (canonical)

| Date | Deliverable | Owner | Status |
|---|---|---|---|
| 2026-04-21 Tue | Andy briefing — command center + deck + this roadmap | Max | ✅ Today |
| 2026-04-22 Wed | Rob Jacobson pre-signing call reply — SEND | Max | 🟥 Drafted, unsent (`docs/legal/rob-reply-draft.md`) |
| 2026-04-22 Wed | Adam Sussman second follow-up | Max | 🟧 Pending |
| 2026-04-22 → 24 | Phase 06 polish — Retell `lookup_vault` custom tool end-to-end on real device | Max + Claude | 🟧 In progress |
| 2026-04-25 Fri | GEICO phone swap SQL run — demo setup | Max | 🟧 Scheduled |
| 2026-04-26 Sat | Demo recording — screen + audio end-to-end, 3 takes | Max + mac-claude-node | 🟧 |
| 2026-04-27 Sun | Patent narrative memo (2 pages) + reshoots | Max + counsel | 🟧 |
| **2026-04-28 Mon** | **Patent demo delivered to counsel** (video + narrative + code walkthrough) | Max → Rob/Adam | 🟥 Hard deadline |
| 2026-04-29 Tue | Counsel draft review; final corrections | Counsel | 🟧 |
| **2026-04-30 Thu** | **Provisional Patent #1 filed — AI call agent with live vault access** | Counsel | 🟥 Hard deadline |
| 2026-05-01 Fri | Entity: Secondo, Inc. (DE C-corp) formation begins | Ned | 🟧 Per Ned 4/17 sequence |
| 2026-05-05 → 09 | Phase 10 Track A — 3 P0 fixes from 4/17 MVP audit | Max | 🟥 TestFlight-blocking |
| 2026-05-12 Mon | TestFlight invite-out — 10 closed testers | Max | 🟧 |
| 2026-05-15 Fri | IP assignment docs — MBACIO LLC → Secondo, Inc. | Ned | 🟧 |
| 2026-05-19 → 23 | Phase 07 — Twilio subaccount + A2P 10DLC + Trust Hub (clock started) | Max | 🟧 Brand PENDING |
| 2026-06-02 → 20 | Phase 09 enterprise-tier spec + first design-partner outreach | Max | 🟦 Post-raise preferred |
| **2026-06-30 Mon** | **Provisional Patent #2 filed — hierarchical compilation layer** | Counsel | 🟥 Hard deadline |
| 2026-Q3 | Priced seed round close — $1.5–3M | Max + counsel | 🟦 |

## Phases (from `.planning/ROADMAP.md` — status as of today)

```
Phase 00 — Consolidation         [██████████] COMPLETE ✓
Phase 01 — Project Standard      [████████░░] LARGELY DONE
Phase 02 — Supabase persistence  [██████████] COMPLETE ✓
Phase 03 — Auth (email + Apple)  [██████████] COMPLETE ✓
Phase 04 — Apple submission      [██████████] COMPLETE ✓  ← iOS prod build shipped 4/17
Phase 05 — OCR scan path         [██████████] COMPLETE ✓  ← VisionKit + MLKit + regex + Gemini fallback
Phase 06 — Voice + AI calling    [███████░░░] ~70%       ← PATENT-CRITICAL, demo Apr 28
Phase 07 — Telephony compliance  [███░░░░░░░] A2P PENDING ← brand review at Twilio
Phase 08 — Affiliate + IAP       [░░░░░░░░░░] not started
Phase 09 — Enterprise tier       [█░░░░░░░░░] spec drafted ← Patent #2
Phase 10 — Engagement/Paywall    [█░░░░░░░░░] plan drafted  ← Track A blocks TestFlight
```

## Legal layer — canonical calendar

| When | Who | Purpose | Status |
|---|---|---|---|
| Fri Apr 17 9:30 AM CDT | Collin Schaefer (OGS Law) | Corporate / startup / trademark — Welvey, Navilum, Secondo | ✅ Done |
| Fri Apr 17 11:30 AM CDT | Ned Scharfenberg (LOES) | Entity formation, IP chain, SAFE guidance | ✅ Done — supersedes prior Welvey plan |
| This week | Rob Jacobson (Marshall Gerstein) | Patent #1 pre-signing call — scope + retainer | 🟥 Reply drafted, not sent |
| This week | Adam Sussman | Parallel patent counsel evaluation | 🟧 Awaiting reply |
| Ongoing | Shelly Principe | E-2 sign-off on structural changes | 🟢 Active |
| Apr 27 – May 1 | Collin Schaefer | Out of office | ℹ️ Plan around |
| Apr 28 | Selected patent counsel | Demo delivery (video + narrative) | 🟥 T-7 |
| **Apr 30** | **Selected patent counsel** | **Provisional Patent #1 filed** | 🟥 T-9 |
| May 1 → | Ned | Secondo, Inc. DE C-corp formation; IP assignment | 🟧 |
| **Jun 30** | **Selected patent counsel** | **Provisional Patent #2 filed** | 🟥 T-71 |

**Counsel directory:**

| Name | Role | Contact |
|---|---|---|
| Ned Scharfenberg | Corporate/Transactional | es@loesllc.com · (312) 826-1980 |
| Collin Schaefer | Corporate/Startup/Trademark | cfschaefer@ogs.law · (312) 725-6274 |
| Rob Jacobson | Patent (Marshall Gerstein) | rjacobson@marshallip.com · (312) 423-3432 |
| Adam Sussman | Patent | asussman0206@mac.com · (630) 207-7440 |
| Shelly Principe | E-2 Immigration | sprincipe@principeimmigrationlaw.com · (888) 907-7462 |
| CPA (replacement) | Startup tax / multi-entity | 🟧 Awaiting Ned referral — Sue Romanoski not a fit |

## Entity structure (post Ned 2026-04-17)

```
MBACIO LLC (IL S-corp, E-2 anchor · existing consulting revenue)
   │
   ├── Navilum, Inc.   (DE C-corp · patent assigned · investor-facing)
   └── Secondo, Inc.   (DE C-corp · patent assigned · investor-facing)
```

Intercompany doc + IP assignment (present + future) handled by Ned. **No Welvey Labs intermediary** — Ned's recommendation supersedes the prior holdco plan. MBACIO retains >50% through two rounds.

## Open / blocked

- 🟥 **Rob Jacobson reply — send today.** Drafted at `docs/legal/rob-reply-draft.md`.
- 🟥 **Phase 10 Track A (3 P0 fixes) blocks TestFlight.** Not demo-blocking, but blocks invite-out.
- 🟧 **Adam Sussman follow-up** pending since 4/15 car call.
- 🟧 **A2P 10DLC Brand** `BN2950da1a655a5d07c65eb7aa27fccc70` still in review at Twilio. Not demo-blocking.
- 🟧 **App icon / splash** still Expo defaults. Logos exist in `OneDrive/_CODE/secondo/logos/`. Counsel will see it.
- 🟧 **Replacement CPA** — awaiting Ned referral. S-corp-owning-C-corps fluency required.

## Reference links

- Command center (Andy briefing): `file:///C:/Users/maxys/Projects/secondo-app/docs/MBACIO-COMMAND-CENTER-2026-04-21.html`
- Investor deck: `file:///C:/Users/maxys/Projects/secondo-app/docs/pitch/secondo-investor-deck-2026-04-21.html`
- Project state: `.planning/STATE.md`
- Phase roadmap: `.planning/ROADMAP.md`
- Apr 28 demo punch list: `.planning/APRIL-28-DEMO-PUNCH-LIST.md`
- Patent claims draft: `docs/deliverables/secondo-patent-claims.html`
- Patent traceability matrix: `docs/PATENT-LAYER-TRACEABILITY.md`
- Demo narrative: `docs/deliverables/demo-narrative.md`
- Rob Jacobson reply draft: `docs/legal/rob-reply-draft.md`
- Enterprise tier spec: `docs/strategy/ENTERPRISE-SECOND-BRAIN-TIER.md`
- Privacy policy (live): `https://wftogyocxaomxmdfqvve.supabase.co/functions/v1/privacy`
- First iOS build: `https://expo.dev/accounts/mbacio/projects/secondo/builds/572b57f0-effb-48e9-af18-788294c0888b`

---
*All metrics and dates in this document trace to a queryable source. No fabricated numbers.*
