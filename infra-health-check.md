# Secondo Outbound Calling Infrastructure — Health Check

**Date:** 2026-04-12  
**Checked by:** Claude Code (automated)  
**Purpose:** Pre-demo infrastructure audit for April 28 patent demo

---

## Summary

| Component | Status | Blocking? |
|-----------|--------|-----------|
| Retell agent alive | ACTIVE (responds to API) | No |
| Agent published | **PUBLISHED** (v4 live, v5 draft — normal) | No |
| Phone number registered | YES (+13128474213) | No |
| Phone number bound to agent | Inbound + outbound bound to agent ✓ | No |
| A2P Brand status | PENDING (voice unaffected — SMS only) | No (voice demo) |
| Privacy policy URL | LIVE — Supabase Edge Function | No |
| Terms of Service URL | LIVE — Supabase Edge Function | No |

---

## 1. Retell Agent

- **Agent ID:** `agent_5fc865d53a476b5d6d258e593c`
- **Agent Name:** Secondo Assistant
- **Voice:** 11labs-Marissa
- **Language:** en-US
- **Channel:** voice
- **Response engine:** retell-llm (LLM ID: `llm_0632ccd805f0fcdcd3d00384f63d`)
- **Max call duration:** 3,600,000 ms (1 hour)
- **Data storage:** everything
- **`is_published`:** `true` — v4 published ✓ (v5 is normal auto-created draft)
- **`version`:** 4 (published), 5 (editable draft)

Agent is live. Retell creates a new draft version automatically on each publish — v5 draft is expected and does not block routing. Live calls route to v4.

---

## 2. Phone Number (+13128474213)

- **Registered in Retell:** YES
- **Type:** retell-twilio (Twilio-backed via Retell)
- **Nickname:** Secondo Demo
- **Area code:** 312 (Chicago)
- **Toll-free:** No
- **Country:** US
- **Inbound agent:** `agent_5fc865d53a476b5d6d258e593c` (agent version 0) — BOUND
- **Outbound agent:** `agent_5fc865d53a476b5d6d258e593c` — BOUND ✓
- **SIP trunk:** `ace4c3a29b8ab81fe665bf872745877606.retell.pstn.umatilla.twilio.com` (TCP)
- **Custom SMS:** disabled

Number is correctly registered and wired for both inbound and outbound. Outbound binding was set 2026-04-12 overnight via `PATCH /update-phone-number`.

---

## 3. A2P Brand (BN2950da1a655a5d07c65eb7aa27fccc70)

**SKIPPED** — Twilio credentials (`TWILIO_ACCOUNT_SID` / `TWILIO_AUTH_TOKEN`) are not present in `.env.local`. The `.env.example` declares `EXPO_PUBLIC_TWILIO_ACCOUNT_SID` as expected but it is not populated.

The brand was registered 2026-04-12 with status PENDING (per `.env.local` comment). A2P registration typically takes 1–5 business days. It affects SMS capabilities only — **not voice calling**. For an outbound voice-only demo on April 28, A2P brand approval is not strictly blocking unless SMS confirmation is part of the demo flow.

To check: add `TWILIO_ACCOUNT_SID` and `TWILIO_AUTH_TOKEN` to `.env.local` (server-side only, non-EXPO_PUBLIC) and re-run the brand check against `https://messaging.twilio.com/v1/a2p/BrandRegistrations/BN2950da1a655a5d07c65eb7aa27fccc70`.

---

## 4. Blocking Issues for April 28 Demo

### ~~BLOCK 1 — Agent not published~~ — RESOLVED 2026-04-12
Published via `POST /publish-agent/agent_5fc865d53a476b5d6d258e593c`. v4 is live. ✓

### ~~BLOCK 2 — No outbound agent binding~~ — RESOLVED 2026-04-12
Bound via `PATCH /update-phone-number/%2B13128474213` with `outbound_agent_id`. ✓

### A2P Brand — NOT BLOCKING for voice demo
Brand `BN2950da1a655a5d07c65eb7aa27fccc70` was PENDING as of 2026-04-12.
**Voice calls are NOT affected by A2P status.** A2P only gates SMS (10DLC messaging).
Check status: https://console.twilio.com/us1/develop/sms/regulatory-compliance/brand-registrations
If post-call SMS is added to the demo, check this first. Otherwise informational only.

---

## 5. Recommended Actions (Priority Order)

1. ~~**Publish the Retell agent**~~ — DONE ✓
2. ~~**Set outbound agent on phone number**~~ — DONE ✓
3. **Run a live end-to-end call test** from the Expo app. Confirm audio, Marissa voice, vault lookup mid-call works. See demo-narrative.md Step 7.
4. **Check A2P brand status** — informational for voice demo. Check Twilio Console if SMS is in scope.
5. **Schedule a dress rehearsal call by April 25** — 3 days before the demo.
6. **Apple Dev account** — once approved, fill `appleTeamId` in eas.json + app.json (5 min) → trigger EAS production build → TestFlight in ~40 min.

---

## 6. What Is Working

- Retell API is reachable and auth key is valid
- Agent `Secondo Assistant` exists with the correct agent ID, voice (11labs-Marissa), and response engine
- Phone number `+13128474213` is registered, Chicago 312 area code, inbound routing to agent is set
- SIP trunk is configured
- Agent max call duration is 1 hour (sufficient for demo)
- Language set to en-US

---

*Health check performed 2026-04-12. Re-run after fixes to confirm ready state.*

---

## EAS Build Readiness

**Date:** 2026-04-12  
**Goal:** TestFlight submission for patent counsel (Rob Jacobson) by April 28

### eas.json — Production Profile

| Field | Expected | Actual | Status |
|-------|----------|--------|--------|
| `ios.distribution` | `"app-store"` | **NOT SET** (key missing from production block) | MISSING |
| `ios.autoIncrement` | `"buildNumber"` | `"buildNumber"` | OK |
| `autoIncrement` (top-level) | `true` | `true` | OK |
| `appleTeamId` | set | **NOT SET** | MISSING |

**Assessment: INCOMPLETE.** Two required fields are absent:

1. `build.production.ios.distribution` must be `"app-store"` — without it EAS defaults to `"internal"` distribution (ad-hoc) and cannot produce an App Store / TestFlight IPA.
2. `build.production.appleTeamId` — needed for EAS to sign with the correct Apple Developer team. Requires the Apple account approval to get the Team ID.

**Fix (once Apple Dev account is approved):**
```json
"production": {
  "distribution": "app-store",
  "autoIncrement": true,
  "ios": {
    "autoIncrement": "buildNumber",
    "distribution": "app-store",
    "appleTeamId": "<YOUR_APPLE_TEAM_ID>"
  },
  ...
}
```

---

### app.json — iOS Config

| Field | Expected | Actual | Status |
|-------|----------|--------|--------|
| `ios.bundleIdentifier` | set | `com.mbacio.secondo` | OK |
| `ios.buildNumber` | set | `"1"` | OK |
| `ios.appleTeamId` | set | **NOT SET** | MISSING |
| `ios.infoPlist.ITSAppUsesNonExemptEncryption` | set | `false` | OK |
| `ios.privacyManifests` | required entry | 4 API types declared | OK |
| `privacyPolicyUrl` (top-level `expo.*`) | `https://mysecondo.com/privacy` | **NOT SET** | MISSING |

**Assessment: INCOMPLETE.** Two fields are absent:

1. `ios.appleTeamId` — cannot be filled until Apple Developer enrollment is approved. Value is the 10-character Team ID from the Apple Developer portal.
2. `privacyPolicyUrl` — required by App Store Connect for apps that access sensitive data (camera, microphone, contacts). The privacy policy HTML exists at `docs/legal/privacy-policy.html` but needs a live URL (e.g., `https://mysecondo.com/privacy` or an equivalent hosted URL). Must be added to the `expo` block in `app.json`.

**Fix (ready to apply now — URL needs to be hosted first):**
```json
"expo": {
  ...
  "ios": {
    ...
    "appleTeamId": "<YOUR_APPLE_TEAM_ID>"
  },
  "privacyPolicyUrl": "https://mysecondo.com/privacy"
}
```

---

### Privacy Manifest (PrivacyInfo.xcprivacy)

**Status: NOT PRESENT** — no `ios/` directory exists in the repo yet (Expo managed workflow; native `ios/` is generated at build time by EAS).

**Assessment:** For Expo managed workflow this is handled via `ios.privacyManifests` in `app.json`, which IS present and contains 4 API type entries (UserDefaults, FileTimestamp, SystemBootTime, DiskSpace). This satisfies Apple's requirement for managed Expo apps — a standalone `PrivacyInfo.xcprivacy` file is only needed for bare workflow projects. **No action required.**

---

### Privacy Policy URL

**Status: DRAFT EXISTS — NOT LIVE.**

- `docs/legal/privacy-policy.html` exists and covers both Navilum and Secondo.
- No live hosting URL is configured. The ROADMAP.md specifies `https://mysecondo.com/privacy` as the target URL.
- The policy is not referenced in `app.json` (`privacyPolicyUrl` key is absent).

**Assessment: BLOCKING for App Store submission.** App Store Connect will reject the binary if `privacyPolicyUrl` is missing. The HTML file needs to be hosted at a stable URL before the EAS production build is submitted.

**Options (in order of effort):**
1. Host `privacy-policy.html` as a GitHub Pages static page (zero cost, ~15 min to set up).
2. Deploy to an existing domain (`mysecondo.com`, `mbacio.com/secondo/privacy`, etc.).
3. Add the raw GitHub Pages URL as a temporary measure and update it when the domain is live.

---

### Build Readiness Summary

| Item | Status | Blocking? | Fix Time |
|------|--------|-----------|----------|
| `eas.json` production profile | `distribution: app-store` added ✓ — `appleTeamId` pending | YES (Team ID) | 5 min once Apple Dev approved |
| `app.json` iOS config | `privacyPolicyUrl` wired ✓ — `appleTeamId` pending | YES (Team ID) | 5 min once Apple Dev approved |
| Privacy manifest (`PrivacyInfo.xcprivacy`) | N/A (Expo managed — `privacyManifests` in app.json covers this) | No | None |
| Privacy policy URL | **LIVE** — `https://hyudrgfqhawehzbktntp.supabase.co/functions/v1/privacy-policy` ✓ | No | Done |
| Terms of Service URL | **LIVE** — `https://hyudrgfqhawehzbktntp.supabase.co/functions/v1/terms-of-service` ✓ | No | Done |
| Apple Developer account | PENDING APPROVAL | YES (blocks everything else) | External dependency |

**Estimated time to TestFlight-ready once Apple Developer account is approved: ~45 minutes.**

Breakdown: 5 min to fill `appleTeamId` in eas.json + app.json → EAS cloud iOS production build (~35–40 min) → available in TestFlight. Privacy policy and ToS are already live. `distribution: app-store` is already set.

*EAS build readiness check: 2026-04-12. Updated overnight 2026-04-12/13 to reflect fixes.*

---

## ElevenLabs TTS Status

**Date:** 2026-04-12

### API Key

| Location | Key Present? |
|----------|--------------|
| `.env.local` (EXPO_PUBLIC_ELEVENLABS_API_KEY) | YES — `sk_46efea6...` (truncated) |
| `.env.example` | Documented (empty placeholder) |
| Azure Key Vault `kv-mbacio-tools` | Not checked — `az` CLI not logged in during this run |

The key is present and properly prefixed `sk_` (ElevenLabs format). The build-log warning `EXPO_PUBLIC_ELEVENLABS_API_KEY is not set — TTS will be silent.` indicates the variable was absent when that particular build was run, not that it's missing from the repo. This can occur if the build was run without `.env.local` loaded (e.g., bare `expo start` before sourcing the env, or an EAS cloud build that didn't have the secret injected).

### Voice Model and Voice ID

From `src/core/voice/ElevenLabsTtsService.ts`:

| Parameter | Value |
|-----------|-------|
| Voice ID | `21m00Tcm4TlvDq8ikWAM` (Rachel — calm, professional) |
| Model | `eleven_turbo_v2_5` |
| Stability | 0.5 |
| Similarity boost | 0.75 |

### Demo Fallback Behavior

If `EXPO_PUBLIC_ELEVENLABS_API_KEY` is not set at runtime:
- Constructor logs `[ElevenLabsTtsService] EXPO_PUBLIC_ELEVENLABS_API_KEY is not set — TTS will be silent.`
- `speak()` returns immediately without calling the API or playing audio
- **No fallback to `expo-speech`** — silence only; no error is thrown to the caller
- Demo will run without voice responses if the key is absent

### Action

No change needed to `.env.local` — the key is already present. To prevent the build-log warning in EAS cloud builds, add `EXPO_PUBLIC_ELEVENLABS_API_KEY` as an EAS Secret in the Expo dashboard (project Settings → Secrets) so it is injected at build time.

---

## vault-lookup E2E Test

**Date:** 2026-04-12  
**Edge Function URL:** `https://hyudrgfqhawehzbktntp.supabase.co/functions/v1/vault-lookup`  
**Test user:** `demo@secondo.app`

### Auth Step

Sign-in as `demo@secondo.app` via Supabase Auth REST API succeeded:

| Field | Value |
|-------|-------|
| Token type | bearer |
| JWT length | 790 chars |
| JWT prefix | `eyJhbGciOiJFUzI1NiIs...` |
| Auth status | SUCCESS |

### Query 1 — "auto insurance policy number"

**Result:** MATCH  
**Response time:** 643 ms

| Field | Value |
|-------|-------|
| Document matched | GEICO Auto Insurance Card |
| Document ID | `c1000000-0000-0000-0000-000000000002` |
| doc_type | insurance_card |
| category | Auto |
| expiry_date | 2026-11-30 |
| relevance_score | 0.625 |
| Snippet preview | `POLICY NUMBER: 4721-88-3301 / EFFECTIVE: 12/01/2025 / EXPIRATION: 11/30/2026 / INSURED: ALEX J MILLER / 900 N MICHIGAN AVE APT 1204 / CHICAGO IL 60611 / VEHICLE: 2023 TESLA MODEL 3...` |

Returned a usable policy number, expiry date, insured name, and vehicle. Snippet quality is high — sufficient for a demo voice response.

### Query 2 — "when does my lease expire"

**Result:** EMPTY (0 results)  
**Response time:** 627 ms

The exact phrasing "when does my lease expire" returned no results, indicating it fell below the similarity threshold for the lease document's embedding. Follow-up query with "lease apartment" immediately returned the document at 0.875 relevance:

| Field | Value |
|-------|-------|
| Document matched | Apartment Lease — 900 N Michigan Ave #1204 |
| Document ID | `c1000000-0000-0000-0000-000000000003` |
| doc_type | lease |
| category | Family |
| expiry_date | 2027-01-31 |
| relevance_score (with "lease apartment") | 0.875 |

**Root cause:** The query "when does my lease expire" is phrased as a question; the embedding model appears to have low similarity with the lease document text when the query contains no noun keywords. Queries with `lease` as a keyword resolve correctly.

**Fix options:**
1. Pre-process queries in the Edge Function to extract keywords before embedding (strip `when does my`, `what is my`, etc.)
2. Lower the similarity threshold in `vault-lookup` (risk: more false positives)
3. Rewrite the demo query to "lease expiration date" or "apartment lease"

### Additional Query — "health insurance"

**Result:** MATCH (2 documents)  
**Response time:** 388 ms

Returned BCBS Health Insurance Card (relevance 0.875) and W-2 (relevance 0.25 — matched on "health insurance" text inside the W-2 box 12 code DD field). Primary match is correct and demo-ready.

### Summary

| Test | Result | Latency | Demo-ready? |
|------|--------|---------|-------------|
| Auth (demo@secondo.app) | PASS | — | YES |
| Query: "auto insurance policy number" | PASS — GEICO card matched | 643 ms | YES |
| Query: "when does my lease expire" | FAIL — 0 results (question phrasing) | 627 ms | NO — use "lease expiration date" instead |
| Query: "lease apartment" (diagnostic) | PASS — Apartment Lease matched | 485 ms | YES |
| Query: "health insurance" | PASS — BCBS card matched | 388 ms | YES |
| Response time overall | All queries under 650 ms | avg ~536 ms | YES (&lt;2s threshold passed) |

**Edge Function is functional.** Latency is excellent (sub-650 ms for all queries). The one failing query ("when does my lease expire") is a phrasing issue, not a data or infrastructure issue — the lease document is in the vault and returns correctly with keyword-based queries. Demo scripts should use noun-phrase queries ("auto insurance policy number", "lease expiration date", "health insurance card") rather than natural-language questions.

*vault-lookup E2E test performed 2026-04-12.*
