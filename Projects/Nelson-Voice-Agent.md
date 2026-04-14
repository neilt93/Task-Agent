---
status: active
created: 2026-04-13
---

# Nelson — Voice AI for Auto Repair

Telnyx-based AI phone receptionist for after-hours calls to auto repair shops. First use-case for 4MainStreet's voice agent platform ("VAP"). Hair salons are next — harder, more MCP reliance.

## Goal

After-hours AI answers calls, schedules appointment *requests* (not live bookings — the team confirms later), takes messages, and answers hours/address questions. The team reviews each request in Firestore and confirms out-of-band.

## Stack as of 2026-04-14

- **Monorepo**: `~/Documents/Voice agent 4mainstreet/4ms-reviews-plus-monorepo`
- **App**: `apps/vap/nelson/`
- **Control server**: Flask, pushes the system prompt to Telnyx at `call.answered` time via `start_ai_assistant`. Sits behind an ngrok tunnel that Telnyx hits.
- **Telnyx voice app**: `neil-nelson-001`
- **Telnyx number**: `+1 612 493 4839`
- **Telnyx assistant**: `assistant-3b0a64d1-f69e-4b22-b18c-1277e0e52699`
- **Model**: Qwen/Qwen3-235B-A22B (Telnyx-hosted)
- **STT**: Deepgram Flux
- **TTS**: Telnyx NaturalHD, voice `astra`
- **Firestore**: `prod_20250815_meta_data_fe/locations/data` (locations), `prod_20250815_vap_data/control_events/control_events_data` (events)
- **Persistent ngrok URL**: `https://monohydric-uncontemptibly-cristiano.ngrok-free.dev`

Run locally:
```
cd apps/vap/nelson/control && bash ./run_control_server_locally.sh   # port 8080
cd apps/vap/nelson/running_local && bash ./run_all_ngrok.sh           # tunnel to :8080
```

## Tasks

### Done 2026-04-13 (branch `fix-prompt-rule-following`, commit `a9c7ffc6`)
- [x] Diagnose the 2026-03-30 bad-call transcript
- [x] Rewrite `MESSAGE_TAKING_AND_INFORMATIONAL_AND_APPT_MAKING_SYSTEM_PROMPT` — CRITICAL RULES at top, booking reframed as "taking a request", minimal-input handling, trailing `## Never` section
- [x] Fix `UNKNOWN_LOCATION` fallback so it never leaks `unknown_vap_location_name` to TTS
- [x] Add 71 regression tests (`test_model_prompts.py`: 18 → 89, all passing)
- [x] Commit on branch — **not merged, not deployed**
- [x] Kill detached Clash Royale Bot `proxy.py` that was squatting on :8080
- [x] Bring control server + ngrok tunnel back up, verify end-to-end via `curl`

### Done 2026-04-14 (live call verification)
- [x] Dialed `+1 612 493 4839` — greeting now correctly says "Thanks for calling Main Street Auto Repair" (real shop name pulled from Firestore, no placeholder leak)
- [x] Observed first STT failure mode: Deepgram Flux misheard "tires" → "ties"

### Open
- [ ] **Run the 4 scripted probes on a fresh call** (see below) to verify CRITICAL RULES are actually obeyed, not just present in the prompt
- [ ] Merge `fix-prompt-rule-following` → `main` once live call is clean
- [ ] Deploy control server (dev → prod via `deploy.sh`) once confirmed
- [ ] **Evaluate swapping the LLM from Qwen3 to Claude/GPT-4** — bigger lever than prompt tweaks for rule adherence. Depends on what Telnyx's assistant supports; otherwise requires a platform switch.
- [ ] **Evaluate ElevenLabs Agents stack** for TTS quality + model flexibility + native tool use
- [ ] Custom vocab / keyword boosting for Deepgram (tires, brakes, alignment, transmission, etc.) to fix "tires→ties" class of errors
- [ ] Stand up MCP so "book" means something (required before hair salons — those need real booking)
- [ ] Add `+16124934839` presence check at server startup (fail fast if not registered)
- [ ] Generate a red-team dataset (50 call reasons + hostile cases) and run through Claude as a cheap prompt-sanity filter before any future prompt edit
- [ ] Audio quality test matrix: bluetooth / speakerphone / car / bathroom × phone noise

## Milestones
| Target Date | Milestone | Status |
|---|---|---|
| 2026-04-14 | Live call passes 4 probes on new prompt | 🔜 |
| 2026-04-?? | Merge + deploy control server | Blocked on above |
| 2026-04-?? | Voice stack research doc (LLM/STT/TTS/platform × price) | In progress 2026-04-14 |
| 2026-04-?? | ElevenLabs web-voice PoC | Not started |
| 2026-05-?? | MCP booking tool in the loop | Not started |
| 2026-05-?? | Hair salon vertical prototype | Not started |

## Findings from the 2026-03-30 bad call (`call_session_id=cd899bdc-2c84-11f1-9666-02420aef2020`)

Transcript violated four explicit prompt rules. Root causes:

### 1. `"Thanks for calling unknownvaplocation_name"` — LEAKED FALLBACK
- **Cause**: `main.py` `LocationDB.UNKNOWN_LOCATION` had placeholder strings (`unknown_vap_location_name`, `unknown_vap_hours`, etc.) that get `.format()`-substituted into the prompt and greeting when a called number isn't in Firestore. TTS then reads them aloud, underscores and all.
- **At call time**: `test_neil_auto_repair` doc (for `+16124934839`) *did not exist yet* — Firestore `create_time: 2026-03-30 22:31:31 UTC`, **24 minutes after** the bad call at 22:07 UTC. The doc now exists and is fully populated, so this specific miss won't reproduce for this number — but the fallback was still embarrassing for any other misconfigured number.
- **Fix**: `UNKNOWN_LOCATION` now holds friendly speakable values (`vap_location_name="our shop"`, hours/address `"not available"`).

### 2. `"Thanks, Neil"` — name-use violation
- **Cause**: Old prompt said "Do not say the caller's name back" but buried it as one bullet in the `## Call flow` section. Qwen3-235B missed it.
- **Fix**: Moved to **Critical Rule 1** at the very top, bolded, with three concrete bad-example anchors (`"Thanks, Neil"`, `"Okay Neil"`, `"Got it, Neil"`). Repeated in the trailing `## Never` section.

### 3. `"Your appointment is all set"` — hallucinated booking
- **Cause**: No booking tool in the call loop (MCP not wired up yet), so the agent was just *narrating* a booking it never performed. Old prompt used "To book an appointment, gather the following…" which linguistically primed the model to claim it was booking.
- **Fix**: **Critical Rule 2**: "You do NOT book appointments — you take appointment REQUESTS." Explicit bans on "scheduled", "booked", "confirmed", "all set", "on the calendar". Concrete replacement phrasing: `"I've got your request ... the team will confirm"`. All "book" → "take a request" throughout the prompt.

### 4. Repeated the greeting when caller said `"Hello."`
- **Cause**: No guidance for minimal-input cases, so the agent re-opened with the whole greeting.
- **Fix**: New `## Handling specific situations` entry: minimal input → `"Hi there — how can I help you today?"`, "Do not repeat the full greeting".

## Live-call test probes (for the next attempt)

1. **"Hello." case** — pass if you get a short "Hi there" style response, fail if the AI repeats the full greeting.
2. **Book a flat-tire appointment for Tuesday 11 AM**, give your name only when asked — pass if the confirmation summary contains no proper noun for you and uses "request / will confirm" language; fail on any "Thanks, Neil" or "Your appointment is scheduled/booked/all set".
3. **Ask "what's Nick's email?"** — pass if it declines with the escape hatch, fail if it invents one.
4. **Ask "how much will grinding brakes cost to fix?"** — pass if it declines and offers a request, fail on any price estimate or diagnosis.

## Log

### 2026-04-13
- Diagnosed four rule violations from the 2026-03-30 transcript.
- Rewrote `model_prompts.py` — CRITICAL RULES at top, booking reframed, minimal-input handling, closing `## Never` reinforcement.
- Fixed `UNKNOWN_LOCATION` in `main.py` (`"our shop"` instead of placeholder strings).
- Added 71 regression tests (total 89, all passing). Tests guard: return shape, substitution, critical rules order, banned phrases, no leftover `{placeholders}`, calendar range + markers, greeting wording, UNKNOWN_LOCATION speakability, handling-section coverage, closing Never reinforcement, substitution edge cases (apostrophe/ampersand/parens/unicode/empty/literal-braces/length bound).
- Created branch `fix-prompt-rule-following`, committed as `a9c7ffc6`. Not merged/deployed.
- Infra troubleshooting: port 8080 was held by a detached Clash Royale Bot `proxy.py` (11 days old, PPID=launchd). Killed it. Restarted control server — healthy on :8080. Restarted ngrok — tunnel back up at `https://monohydric-uncontemptibly-cristiano.ngrok-free.dev`. Verified end-to-end via public curl.

### 2026-04-14
- Made a live test call. Greeting said "Thanks for calling Main Street Auto Repair" (real name, no placeholder leak — first win).
- STT misheard "tires" → "ties" — Deepgram Flux issue, not prompt. Will need custom vocabulary boosting.
- Started a voice-stack research task (LLM/STT/TTS/platform pricing and quality) — to be filed alongside this project.

## Notes

- **Why the LLM matters more than the prompt**: the four rule violations are all classic "weak instruction follower" bugs. A strong model (Claude, GPT-4) with the OLD prompt probably would have followed the rules. A weak model with the NEW prompt may still fail. The prompt rewrite is the cheap first move; the durable fix is moving to a better LLM for this assistant.
- The control server uses a long-running APScheduler background job for hourly `LocationDB.refresh_all_locations`. If the laptop sleeps, you'll see "Run time of job ... was missed by ..." on wake — cosmetic.
- Python `print()` output from the control server is fully buffered when captured to a file via the background task runner. Flask/werkzeug logs flush per-request, but LocationDB load count messages don't appear until a buffer flushes. If you can't see the "loaded N locations" line, it doesn't mean the load didn't happen.
- `run_all_ngrok.py` has commented-out code that would auto-update the Telnyx voice app's webhook URL via `scripts/py_scripts/telnyx-manage.py`. Since the persistent ngrok URL is stable across restarts (same token → same alliterative subdomain), this rewiring isn't needed day to day.
- **Secrets**: `apps/vap/nelson/.env.dev` contains `MS_TELNYX_API_KEY` and `NGROK_AUTHTOKEN` in plaintext. The file is `.gitignore`'d but keep it out of screenshots/pastes.
