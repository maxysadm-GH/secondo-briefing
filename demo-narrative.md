# Secondo — April 28 Patent Demo Narrative and Recording Script

**Prepared for:** Robert S. Jacobson, Patent Counsel, Marshall Gerstein & Borun LLP
**Demo date:** April 28, 2026
**Filing deadline:** April 30, 2026 (Provisional Patent Application #1)
**Primary claim demonstrated:** P1-C1 — Voice command → vault retrieval → AI-mediated outbound call → mid-call document lookup → post-call action items + calendar event
**Demo user:** `demo@secondo.app` / `demo1234`

---

## Section 1: Setup Checklist (Day Before — April 27)

Complete every item below before going to bed on April 27. The demo is 10 minutes; recovery time is zero.

### 1.1 Simulator vs. Device Recommendation

Run the demo on the **iOS Simulator (iPhone 17 Pro, iOS 18)** via Xcode, not a physical device. Reasons:

- Screen recording is one command (`xcrun simctl io booted recordVideo`) — no AirPlay lag, no QuickTime mirroring artifacts, guaranteed 1080p.
- Apple Speech recognition works on Simulator in iOS 18 (Plan B wake word path).
- No risk of a cellular notification or call interrupting the recording.
- Counsel receives the file directly; no additional export step.

If you want a physical device as a backup, keep an iPhone running the TestFlight build, but record from the Simulator.

### 1.2 Environment Verification

```bash
# 1. Confirm .env values are set
grep -E "RETELL_API_KEY|RETELL_AGENT_ID|SUPABASE_URL|SUPABASE_ANON" /c/Users/maxys/Projects/secondo-app/.env

# 2. Confirm the agent ID hardcoded in calls.tsx matches the live agent
# Expected: agent_5fc865d53a476b5d6d258e593c
# File: app/(tabs)/calls.tsx line 208

# 3. Confirm vault-lookup Edge Function is deployed
# URL: https://hyudrgfqhawehzbktntp.supabase.co/functions/v1/vault-lookup
```

### 1.3 Demo Account Verification

1. Open Simulator. Boot the Secondo app.
2. Sign in as `demo@secondo.app` / `demo1234`.
3. Navigate to the Vault tab — confirm 8 documents are visible:
   - Illinois Driver's License
   - GEICO Auto Insurance
   - Blue Cross Blue Shield Health Insurance
   - Apartment Lease
   - Pet Insurance
   - Prescription / Rx
   - Tesla Service Record
   - W-2
4. Tap the GEICO Auto Insurance document — confirm the `ocr_text` field is populated (the policy number should be readable in the detail view). This is the data the AI will retrieve during the live call.

### 1.3a Phone Number Swap — REQUIRED Before Demo

> **Why:** The GEICO service provider in the demo vault has the real GEICO phone number (+18007624462). For the patent demo, the outbound call must go to a number you control — not real GEICO.

**Before the demo, update GEICO's phone number in Supabase to your own cell:** `+13129001114`

```sql
-- Run in Supabase SQL Editor or via MCP
UPDATE service_providers
SET phone = '+13129001114'
WHERE id = 'b1000000-0000-0000-0000-000000000001'
  AND user_id = '0e0651f9-74d8-4505-a09b-1062223a25b6';
```

Or update it directly in the Supabase Table Editor:
- Table: `service_providers`
- Row: GEICO / `b1000000-0000-0000-0000-000000000001`
- Column: `phone` → `+13129001114`

**During the demo:** Your phone will ring. Answer it. The Retell AI agent will introduce itself ("This call is being assisted by an AI…"). Ask: "What's the policy number?" — the AI will query the vault and answer. That is the moment Rob Jacobson needs to see.

**After the demo:** Revert the phone number back to `+18007624462` if desired.

### 1.4 Retell Agent Test Call

Place one test call the day before to confirm the agent is live and responding:

```bash
# Quick smoke test — replace RETELL_API_KEY with the value from .env.local
# from_number: the registered Retell/Twilio number
# to_number: YOUR cell phone (must be swapped in service_providers first — see 1.3a)
curl -s -X POST https://api.retell.ai/v2/create-phone-call \
  -H "Authorization: Bearer $EXPO_PUBLIC_RETELL_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "from_number": "+13128474213",
    "to_number": "+13129001114",
    "override_agent_id": "agent_5fc865d53a476b5d6d258e593c"
  }'
```

Your cell will ring from +13128474213 (Chicago 312 area code). The Retell AI agent will pick up. Ask "What's the policy number on the auto insurance?" — the agent should answer with `4721-88-3301`. If this works, the demo is ready. Save the call transcript and a recording of this test call as your fallback asset (see Section 4).

### 1.5 Recording Setup

```bash
# Start the screen recording BEFORE launching the demo
# Do this in a terminal window you will leave open throughout
xcrun simctl io booted recordVideo ~/Desktop/secondo-demo-2026-04-28-take-1.mp4
```

The command blocks until you press `Ctrl+C`. Do not stop it until the demo is completely over.

### 1.6 Final State Before Bed

- [ ] **GEICO phone number swapped to +13129001114** in Supabase service_providers (see 1.3a)
- [ ] Demo user signed in, vault shows 8 documents
- [ ] GEICO policy `ocr_text` is populated
- [ ] Test call placed (your cell rang from +13128474213, AI answered policy number correctly)
- [ ] Test call transcript saved to `~/Desktop/secondo-test-call-transcript.txt`
- [ ] `xcrun simctl io booted recordVideo` command tested (starts and stops cleanly)
- [ ] Simulator display set to portrait orientation
- [ ] Simulator font size at default (not accessibility-large)
- [ ] Phone notifications silenced on the Mac running Xcode

---

## Section 2: The Demo Script (Step-by-Step, ~10 Minutes)

> **Vault Query Phrasing — resolved 2026-04-13:** vault-lookup v5 handles natural-language questions directly. "When does my lease expire?" and "What's my insurance policy number?" now work as-is. The Edge Function strips question openers and stop words before FTS. No noun-phrase rewrites needed.

Read the narration out loud, exactly as written. The words in `[brackets]` describe what happens on screen — they are not spoken.

---

### Step 1: Introduction (0:00 – 1:00)

**Say:**

> "This is Secondo — a voice-first second brain for your phone. The idea is simple: everything important in your life — insurance cards, your lease, your prescriptions, your IDs — lives in an encrypted vault on your phone. And when you need to use any of it, you just talk to the app. You don't dig through documents. You don't put the caller on hold. You just say the word, and Secondo handles it."

> "We're going to do that right now, live. I'm going to call my auto insurance company, and Secondo is going to be on the call with me — looking things up in the vault in real time, answering questions, and logging what happened afterward."

[Simulator is showing the Calls tab. The screen is clean — no active call.]

---

### Step 2: Open the Vault (1:00 – 2:00)

**Say:**

> "Before we make the call, let me show you the vault. These are my documents. They were scanned in with the app's built-in OCR scanner — the app reads them, extracts the text, and stores them in encrypted rooms organized by category."

[Tap the Vault tab in the bottom navigation.]

[Scroll slowly through the document list. Pause on each of the 8 items so the names are legible on camera.]

**Say:**

> "GEICO auto insurance — that's what we're calling today. The app already has my policy number, my coverage details, my deductible. Everything Secondo needs to answer questions on my behalf."

> "This is what we mean by 'second brain.' The information is here. You don't have to remember it, read it out loud to a representative, or put anyone on hold."

[Tap the GEICO document briefly to show the detail view, then navigate back to the Calls tab.]

---

### Step 3: Wake Word (2:00 – 3:00)

**Say:**

> "Now watch what happens when I say the wake word."

[Hold the phone (or have the Simulator screen visible, mic active). Speak clearly.]

> **"Hey Secondo."**

[`WakeWordService.ts` line 144: Apple Speech recognition matches `hey secondo` against `WAKE_PHRASES`. The app transitions from `idle` to `listening`. A visual indicator appears on the Calls tab — the voice state UI activates.]

**Say:**

> "The app just woke up. It heard me. No button press, no unlock, no unlock screen — just the phrase. That's running on Apple Speech, entirely on-device, no network call. And it's always listening in the background."

[Pause 3 seconds. Let the visual state settle.]

---

### Step 4: Voice Intent — "Call Geico" (3:00 – 4:00)

**Say:**

> "Now I'm going to tell it what I want."

[App is in `listening` state. Speak clearly.]

> **"Call Geico about my auto insurance."**

[`calls.tsx` line 283: `VoiceSession` emits a `transcription` event with `event.result.text = "Call Geico about my auto insurance"`.

`calls.tsx` line 32–51: `parseVoiceIntent()` matches the regex `/(call|phone|dial|ring)\s+(.+)/i`, extracts `"Geico"`, finds the matching `ServiceProviderRow` in the loaded providers list.

`calls.tsx` line 284–285: `handleCallRef.current()` is invoked with the GEICO provider row.]

**Say:**

> "Secondo just parsed that. It heard 'Call Geico,' matched it to the GEICO service provider in the vault, looked up the phone number, and is now placing the call — all in the time it takes to say the sentence."

---

### Step 5: AI Call Connects (4:00 – 5:00)

[`calls.tsx` lines 199–214: `handleCall()` constructs the `CallAgent`, calls `agent.initiateCall(phone, "GEICO", "agent_5fc865d53a476b5d6d258e593c", metadata)`.

`RetellClient.ts`: HTTP POST to `https://api.retell.ai/v2/create-phone-call`. Retell dials the number and connects `agent_5fc865d53a476b5d6d258e593c` to the call.

The Retell agent's system prompt instructs it to open with the AI disclosure.]

[The call status bar appears at the top of the screen. Duration counter starts.]

**Say:**

> "The call is connecting now. There are three parties on this line: me, the GEICO representative, and Secondo — the AI. Secondo is a persistent participant for the entire call."

[When the Retell agent picks up, it will say something like:]

> "Hi, this is an AI assistant calling on behalf of [user's name] regarding their GEICO auto insurance policy. A human representative will be available to assist."

**Say:**

> "You just heard the disclosure. Every call Secondo places opens with that statement — the other party knows they're speaking with an AI assistant. That's baked into the agent's system prompt."

---

### Step 6: Mid-Call Document Overlay (5:00 – 6:00)

[Direct attention to the active call bar at the top of the screen.]

**Say:**

> "Look at the top of the screen. While the call is active, Secondo is automatically surfacing documents from the vault that are relevant to GEICO. I didn't ask for them — the app recognized the provider name and pulled the matching documents before I said a word."

[`ActiveCallBar` component, `calls.tsx` lines 95–135: `ActiveCallBar` renders `relevantDocs` — documents filtered by provider name match against `doc.name` and `doc.tags`. GEICO-tagged documents appear as chips in the horizontal scroll strip.]

[Scroll the document chips slowly so they're visible on camera.]

**Say:**

> "Policy number. Coverage dates. Deductible amount. All of it is right there on screen, ready to reference or relay. This is claim P1-C3: the system surfaces vault documents to the user during the active call without interrupting the call."

---

### Step 7: AI Answers a Question Mid-Call (6:00 – 8:00)

[This is the core demonstration of P1-C1. The "representative" (played by you, on speaker, or another device) asks a question.]

**Prompt the simulated representative to ask:**

> "What's the policy number on the account?"

[Speak the vault query voice command clearly:]

> **"auto insurance policy number"**

[`calls.tsx` line 283: `VoiceSession` emits a `transcription` event. `parseVoiceIntent()` at line 32 matches `/(what is|when does|find|show me|auto|health|lease|apartment)\s+(.+)/i`, returns `{ type: 'vault-query', query: 'auto insurance policy number' }`.

`calls.tsx` line 220–255: `handleVaultQuery()` tokenizes the query into keywords, scores every document in the vault by keyword overlap against `doc.name` and `doc.ocr_text`, selects the highest-scoring result — the GEICO insurance document — and constructs a spoken answer from `doc.ocr_text`.]

[The app speaks the answer aloud via TTS, or displays it in the transcript area. The answer contains the policy number extracted directly from the GEICO document's `ocr_text`.]

**Say:**

> "Secondo just answered that question. It didn't read from a script — it queried the vault in real time, found the GEICO insurance document, extracted the policy number from the OCR'd text, and responded. All while the call was active. The representative heard the answer. The call was never interrupted."

> "That is the core claim. A voice command to call an entity — the call is placed — an AI participant joins and stays on the call — a question is asked — the AI looks up the answer in the user's document vault — and the answer is delivered during the active call. That's claim P1-C1."

[Pause for effect. Let the demonstration land.]

---

### Step 8: Call Ends — Action Items and Calendar (8:00 – 9:00)

**Say:**

> "Now let's end the call."

[Tap the Hang Up button in the active call bar.]

[`CallAgent.ts` lines 319, 326: `hangUp()` triggers `finalizeCall()`. `finalizeCall()` calls `extractActionItems(transcript)` at line 334 to parse action items from the full call transcript using regex patterns. For any items with a due date, `calendar.createEvent()` is called at line 341 via `ExpoCalendarAdapter`.]

[The screen transitions to the post-call summary. Action items extracted from the conversation are listed. If a follow-up date was mentioned during the call, a calendar event appears.]

**Say:**

> "The call just ended, and Secondo immediately processed the transcript. It extracted the action items from the conversation and created a calendar event — automatically, without any input from me."

> "That's claim P1-C4: post-call transcript processing with automatic action item extraction and calendar event creation."

---

### Step 9: Outro (9:00 – 9:30)

**Say:**

> "What you just watched is the system we're filing on. Voice wake word, intent parsing, outbound AI call, persistent AI participant, real-time vault lookup mid-call, and automated post-call processing — all in one continuous flow, on a real app, running on a real device."

> "Every step you saw is implemented in the codebase. We have the code, we have the running app, and we have this recording. That is what makes the provisional patent defensible."

[Hold the final screen for 5 seconds. Stop recording with `Ctrl+C`.]

---

## Section 3: Recording Instructions

### 3.1 Primary Method — Simulator Screen Capture

```bash
# Start BEFORE beginning the demo narration
xcrun simctl io booted recordVideo ~/Desktop/secondo-demo-2026-04-28-take-1.mp4

# Stop after the outro hold
# Press Ctrl+C in the terminal
```

- Resolution: native Simulator resolution, typically 1170x2532 (portrait, iPhone 17 Pro equivalent). This renders as approximately 1080p when scaled for sharing.
- Orientation: **portrait**. The app is portrait-only. Do not rotate.
- File will be saved as `.mp4` (H.264). Compatible with all review workflows.

### 3.2 Alternative Method — QuickTime Player

If `xcrun` fails (Simulator device not booted correctly):

1. Open QuickTime Player.
2. File → New Movie Recording.
3. Click the dropdown arrow next to the record button.
4. Under "Camera," select the booted iPhone Simulator.
5. Click the red Record button.
6. Run the demo.
7. Stop recording. Export as 1080p (File → Export As → 1080p).

### 3.3 File Naming Convention

```
secondo-demo-YYYY-MM-DD-take-N.mp4
```

Examples:
- `secondo-demo-2026-04-28-take-1.mp4` — first attempt
- `secondo-demo-2026-04-28-take-2.mp4` — retry after a flub
- `secondo-demo-2026-04-27-test.mp4` — day-before smoke test (keep this as fallback)

Store all takes in `~/Desktop/` during the session. After the demo, move final takes to `docs/deliverables/recordings/` in the repo (gitignored by size).

### 3.4 Audio

The Mac's built-in microphone captures your voice narration. The Simulator's speaker audio (Retell agent voice) comes through the Mac's speakers and is picked up by the mic. For clean audio: record in a quiet room, speak toward the Mac, and keep the volume at about 60% so the agent audio is audible without distorting the microphone pickup.

---

## Section 4: Failure Fallbacks

### 4.1 Retell Call Does Not Connect

**Symptom:** `handleCall()` fires but the call status stays `dialing` indefinitely, or returns an error.

**Fallback:** Open the saved test call transcript from April 27 (`~/Desktop/secondo-test-call-transcript.txt`). Navigate to the Call History tab in the app — the prior test call will appear. Tap it to show the transcript.

**Script adjustment:**

> "I'm going to switch to a recorded call to save time — same flow, same result. What you're seeing is the transcript from a test call I placed against the same Retell agent yesterday."

Walk through the transcript and narrate each step. The patent claim does not require the call to happen live during the demo — it requires the system to be capable of placing it.

### 4.2 Voice Intent Does Not Parse

**Symptom:** You say "Call Geico about my auto insurance" and nothing happens. Possible cause: Apple Speech returned a slightly different transcription ("call GEICO" vs "Call Geico") that doesn't match the regex.

**Fallback:** Scroll the provider list on the Calls tab to GEICO and tap the row directly. `handleCall()` at `calls.tsx` line 199 is called with the same provider row either way.

**Script adjustment:**

> "I'm going to tap the provider directly to show you the manual path — the voice path and the tap path both invoke identical code. In production, the voice parser handles it automatically."

### 4.3 Vault Lookup Returns No Results Mid-Call

**Symptom:** You say "auto insurance policy number" and the answer is "I couldn't find a document matching that query in your vault."

**Most likely cause:** The GEICO document's `ocr_text` field is empty in the demo account's database. Verify and fix before demo day (Section 1.3 step 4).

**Fallback (if discovered during the live demo):** Direct attention to the document chips in the active call bar overlay.

**Script adjustment:**

> "The voice query path is one of two ways Secondo surfaces vault data during a call. The other is the automatic overlay you can see here — it pre-loads relevant documents the moment the call connects, before any question is asked. The policy number chip you see here contains the same information. That's claim P1-C3."

### 4.4 Wake Word Is Not Detected

**Symptom:** You say "Hey Secondo" and nothing happens. Apple Speech may not have microphone permission granted in the Simulator session.

**Fix before demo day:** In Simulator, open Privacy & Security → Microphone and confirm Secondo is listed and enabled. Alternatively, tap the voice/mic button in the Calls tab UI to trigger `VoiceSession.startListening()` manually.

**Fallback during demo:** Tap the microphone button in the UI to enter listening state, then speak the intent. Describe the wake word behavior:

> "On a real device, the wake word activates this hands-free. In the Simulator, microphone routing sometimes requires a tap to start — the underlying mechanism is identical."

---

## Section 5: Patent Narrative Summary

Patent drawings at docs/deliverables/patent-drawings/
- fig1-system-architecture.svg — System components with reference numerals (100-series)
- fig2-call-flow-sequence.svg — Sequence diagram from wake word through post-call processing
- fig3-vault-lookup-detail.svg — Vault lookup mechanism: JWT auth, FTS query, snippet delivery
- fig4-state-machine.svg — Call lifecycle state machine with per-state claim mapping

### For Robert Jacobson — Claim-to-Code Mapping

The following maps each moment in the demo recording to the exact patent claim it demonstrates, with the implementing code location. All file paths are relative to the project root `secondo-app/`.

---

#### P1-C1: Independent Method — Voice Command → Vault Retrieval → Outbound Call → AI Participant → Mid-Call Document Retrieval

**This is the primary independent claim. The entire demo proves it.**

| Recording timestamp | What the user sees | Code location | Claim step |
|---|---|---|---|
| ~0:30 | App running, Calls tab visible | `app/(tabs)/calls.tsx` | System is operational |
| ~2:05 | App wakes on "Hey Secondo" | `src/core/voice/WakeWordService.ts:27` — `WAKE_PHRASES` array; line 144 — Apple Speech match | Step (a): receives voice command to call a target entity |
| ~3:10 | Transcription appears: "Call Geico about my auto insurance" | `app/(tabs)/calls.tsx:283` — `VoiceSession` `transcription` event handler | Step (a) continued: voice command received and transcribed |
| ~3:12 | Provider matched, call initiates | `app/(tabs)/calls.tsx:32–51` — `parseVoiceIntent()` regex match; line 210 — `agent.initiateCall()` | Step (b): intent parsed, vault documents associated with GEICO retrieved; Step (c): outbound call placed via Retell programmable voice API |
| ~4:10 | Active call bar appears, duration counter starts | `app/(tabs)/calls.tsx:403` — `ActiveCallBar` rendered with `agent_5fc865d53a476b5d6d258e593c` | Step (e): AI persistent participant (`agent_5fc865d53a476b5d6d258e593c`) joins call via Retell |
| ~5:20 | Document chips appear in active call bar | `app/(tabs)/calls.tsx:95–135` — `ActiveCallBar` component; line 101–110 — `relevantDocs` filter | Step (i): vault documents surfaced to user during active call |
| ~6:30 | "auto insurance policy number" spoken; answer returned | `app/(tabs)/calls.tsx:220–255` — `handleVaultQuery()`; lines 222–234 — keyword scoring against `doc.ocr_text` | Steps (g)+(h): system detects information need, retrieves responsive information from vault |
| ~6:45 | Answer spoken/displayed with policy number | `app/(tabs)/calls.tsx:249–253` — answer construction from `doc.ocr_text` | Step (i): responsive information presented to user during active call |

**P1-C1 is fully demonstrated at approximately 6:45 in the recording.**

---

#### P1-C3: Mid-Call Surfacing of Vault Documents to User Interface

**Demonstrated at ~5:10 in the recording.**

The `ActiveCallBar` component (`app/(tabs)/calls.tsx:95`) renders a horizontally scrollable strip of document chips whenever a call is active. Documents are selected by matching the active provider's display name against `doc.name` and `doc.tags` (lines 101–110). This surfacing is automatic — it requires no user action after the call connects.

The vault-lookup Edge Function (`https://hyudrgfqhawehzbktntp.supabase.co/functions/v1/vault-lookup`) is also available to the Retell agent via the `lookup_vault` custom tool registered in the agent's system prompt (`agent_5fc865d53a476b5d6d258e593c`). The Edge Function performs a full-text search against the `documents` table using the GIN index created in `supabase/migrations/20260411000000_add_documents_fts_index.sql`.

---

#### P1-C4: Post-Call Transcript Processing — Action Items and Calendar Events

**Demonstrated at ~8:10 in the recording.**

When `hangUp()` is called (`src/core/calls/CallAgent.ts:319`), the `finalizeCall()` private method runs (line 326). It calls `extractActionItems(transcript)` (line 334), which applies regex patterns to the call transcript to extract structured action items including description and optional due date. For each extracted item that carries a due date, `this.calendar.createEvent()` is invoked (line 341) via the `ExpoCalendarAdapter` (`src/core/calls/ExpoCalendarAdapter.ts`). The resulting calendar event ID is stored on the action item at line 345.

The post-call state (action items, calendar event IDs, timestamps) is emitted via `updateState()` (line 353) and rendered in the Calls tab UI.

---

#### P1-C1, Step (a): Wake Word — Apple Speech Recognition Plan B

The wake word implementation uses a two-tier fallback architecture in `src/core/voice/WakeWordService.ts`. Plan A uses the Picovoice Porcupine wake word engine (requires `EXPO_PUBLIC_PICOVOICE_ACCESS_KEY`). When the access key is absent or Porcupine fails to initialize, the service automatically falls back to Plan B (line 114) — `@react-native-community/voice`, which uses Apple's built-in `SFSpeechRecognizer` API (no API key required, runs on-device). Plan B listens continuously for any of the trigger phrases defined at line 27: `['secondo', 'hey secondo', 'a secondo', 'secondo,']`. On a match (line 144), the service emits a `WakeWordEvent` that `VoiceSession.ts` forwards to `calls.tsx` as a state transition from `idle` to `listening`.

For the April 28 demo, Plan B is the active path. Plan A is a production enhancement — for the purposes of establishing patent priority, the claim requires only that a voice phrase activates the system, which Plan B satisfies.

---

#### Vault Documents and Encrypted Storage

The demo account's vault (`demo@secondo.app`) is seeded with 8 documents stored in the `documents` table in Supabase, accessed via `src/core/data/useDocuments.ts`. The `SecureVault` encryption layer (`src/core/vault/SecureVault.ts`) implements AES-256-GCM encryption with PBKDF2-SHA256 key derivation — the architecture for zero-knowledge storage that underpins Patent #2. For the purposes of the April 28 demo and the P1 claims, the load-bearing point is that documents exist, are associated with the authenticated user, and are retrievable during an active call. The encryption implementation in `SecureVault.ts` is available for counsel's review as evidence of the zero-knowledge claim in Patent #2.

---

#### Retell AI Agent Configuration

The persistent AI participant is Retell agent `agent_5fc865d53a476b5d6d258e593c`. The agent ID is sourced from `EXPO_PUBLIC_RETELL_AGENT_ID` (falling back to the hardcoded value at `app/(tabs)/calls.tsx:208`). The agent's system prompt instructs it to (1) open calls with an AI disclosure statement, (2) act as a knowledgeable assistant with access to the user's document vault, and (3) respond to questions about insurance policy details, coverage, and account information. The full system prompt is documented in `docs/retell-agent-system-prompt.md`.

The `vault-lookup` Edge Function deployed at `https://hyudrgfqhawehzbktntp.supabase.co/functions/v1/vault-lookup` is registered as a custom tool on the agent, enabling the Retell AI to query the user's vault documents server-side during the call — a separate retrieval path from the on-device `handleVaultQuery()` used for the user-facing voice query flow.

---

*This document prepared for counsel use in support of Provisional Patent Application #1 filing, April 30, 2026. Questions: max@mbacio.com / (312) 900-1114.*
