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
- **Control server**: Flask, pushes the system prompt to Telnyx at `call.answered` time via `start_ai_assistant`. Sits behind an ngrok tunnel that Telnyx hits. Also reverse-proxies `/mcp` to the local fastmcp server so a single ngrok tunnel covers both webhook and tool calls.
- **MCP server**: fastmcp on `:8081`, exposes `make_booking` and `take_message`. Started via `apps/vap/nelson/_unused_mcp/run_mcp_server_locally.sh` (yes, still called `_unused_mcp` — it isn't unused anymore, rename pending).
- **Telnyx voice app**: `neil-nelson-001`
- **Telnyx number**: `+1 612 493 4839`
- **Telnyx assistant**: `assistant-3b0a64d1-f69e-4b22-b18c-1277e0e52699` — now named `neil-nelson-001` (was `neil-nelson-001-no-mcp`)
- **Telnyx MCP server**: `069d43b4-1b1a-44dd-875b-8abe13ddedad`, named `neil-nelson`, points at `https://monohydric-uncontemptibly-cristiano.ngrok-free.dev/mcp`
- **Model**: `anthropic/claude-haiku-4-5` (was `Qwen/Qwen3-235B-A22B`)
- **STT**: Deepgram Flux — keyterm prompting not yet enabled (Telnyx API does not surface the `keyterm` field; support ticket required)
- **TTS**: Telnyx NaturalHD, voice `astra`
- **Firestore**: `prod_20250815_meta_data_fe/locations/data` (locations), `prod_20250815_vap_data/control_events/control_events_data` (events)
- **Persistent ngrok URL**: `https://monohydric-uncontemptibly-cristiano.ngrok-free.dev`

Run locally (in this order):
```
cd apps/vap/nelson/_unused_mcp && bash ./run_mcp_server_locally.sh   # port 8081
cd apps/vap/nelson/control && bash ./run_control_server_locally.sh   # port 8080 (proxies /mcp to :8081)
cd apps/vap/nelson/running_local && bash ./run_all_ngrok.sh          # single tunnel -> :8080
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

### Done 2026-04-14 (live call verification + wire-up)
- [x] Dialed `+1 612 493 4839` — greeting now correctly says "Thanks for calling Main Street Auto Repair" (real shop name pulled from Firestore, no placeholder leak)
- [x] Observed first STT failure mode: Deepgram Flux misheard "tires" → "ties"
- [x] Research agent run — findings filed at `Projects/Nelson-Voice-Stack-Research.md`. Headline: stay on Telnyx, swap the LLM, don't migrate.
- [x] Added Flask `/mcp` reverse proxy to the control server (single ngrok tunnel covers both `/webhook` and `/mcp`, since ngrok free tier reserves one domain per account)
- [x] Started fastmcp server locally on `:8081`; verified full MCP Streamable HTTP session end-to-end through the public URL (`initialize` → `tools/list` returns both tools)
- [x] Created Telnyx MCP server resource `neil-nelson` (id `069d43b4-…`) pointing at `https://monohydric-…/mcp`
- [x] PATCHed the assistant: renamed to `neil-nelson-001`, model swapped to `anthropic/claude-haiku-4-5`, MCP server attached
- [x] Updated prompt to inject the call's `call_control_id` as `call_id` and document `make_booking` / `take_message` tool use
- [x] Added 7 MCP-tool regression tests (`test_model_prompts.py`: 89 → 96 tests, all passing)
- [x] Committed wire-up as `2fefe165` on `fix-prompt-rule-following` — **still not merged, still not deployed**
- [x] Live-probed every assistant-recommended model to see what Telnyx actually accepts (see "LLM options" below)
- [x] Attempted `transcription.settings.keyterm` on the PATCH — Telnyx silently dropped the field (API does not currently expose it)

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

## Research summary (2026-04-14)

Full doc: [[Nelson-Voice-Stack-Research|Projects/Nelson-Voice-Stack-Research]].

**One-line recommendation: stay on Telnyx, swap the LLM.** Every observed Nelson problem is fixable without a platform migration.

1. **Rule following** — the `"Thanks, Neil"` / `"all set"` violations are classic weak-instruction-follower bugs. Qwen3-235B is good at reasoning, weak at honoring natural-language rules without few-shot examples. Claude-family models are the industry gold standard for instruction following. **Telnyx natively supports `anthropic/claude-haiku-4-5`** as of late 2025 — this is a single-field PATCH, no migration. Haiku 4.5 is priced at $1/$5 per MTok (vs Qwen3's $0.60/$2.00) — ~$0.0015 more per call, trivial next to the $0.16 observed cost.
2. **STT errors (`tires → ties`)** — Deepgram Flux supports **Keyterm Prompting** (up to 100 domain terms, up to 625% entity-recognition uplift, free). This is a config knob, not a provider swap. Caveat: Telnyx's AI Assistant API does not currently surface Deepgram's `keyterm` field through `transcription.settings` — confirmed by silently-dropped PATCH. Either file a Telnyx support ticket or bypass Telnyx's STT bundle and run Deepgram direct.
3. **Hallucinated bookings** — Claude-family models rarely hallucinate tool calls when a real tool exists in the loop. Wiring up the MCP server with `make_booking` / `take_message` (done this session) plus moving to Haiku should end the problem together.
4. **TTS naturalness** — Telnyx NaturalHD `astra` is fine. If it still sounds robotic after (1)-(3) land, ElevenLabs Flash v2.5 is available inside Telnyx's TTS dropdown for ~$0.025/call extra — cheaper than a migration.
5. **Do NOT migrate to Vapi, Retell, or OpenAI Realtime for cost reasons.** Retell is more expensive than current; Realtime is ~10× current. ElevenLabs Agents is cheaper per call but payback on migration dev cost is 12-20+ years at likely volumes. Only migrate if Telnyx *cannot* surface Flux keyterm config or *cannot* integrate the TTS you want.

## LLM options actually available on Telnyx AI Assistants

Live-probed by PATCHing the `neil-nelson-001` assistant with every model the `/ai/models` endpoint marks `recommended_for_assistants: true`, plus a bunch of common names. Results (PATCH status code):

| Model | Status | Notes |
|---|---|---|
| `anthropic/claude-haiku-4-5` | ✅ 200 | **currently set** — recommended |
| `Qwen/Qwen3-235B-A22B` | ✅ 200 | original, weak instruction follower |
| `openai/gpt-4o` | ✅ 200 | alternative for A/B |
| `openai/gpt-4.1` | ✅ 200 | alternative for A/B |
| `openai/gpt-5` / `gpt-5.1` / `gpt-5.2` | ✅ 200 | fresh — not in research doc |
| `moonshotai/Kimi-K2.5` | ✅ 200 | open-weights, ~Qwen3 pricing tier |
| `google/gemini-2.5-flash` | ❌ 400 | marked recommended but PATCH rejected — likely BYOK required |
| `Groq/kimi-k2-instruct` | ❌ 400 | same |
| `Groq/llama-4-maverick-17b-128e-instruct` | ❌ 400 | same |
| `openai/gpt-4o-mini` | ❌ 422 | **"not available for AI Assistants"** — Telnyx does not offer this model tier through the Assistants API, so the A/B test you asked for can't happen here. Closest alternative is `gpt-4o` (bigger) or `gpt-4.1-mini` (also rejected). |
| `anthropic/claude-sonnet-4-*`, `claude-opus-4-*` | ❌ 422 | not surfaced for Assistants API despite being in `/ai/models`. BYOK workaround possible but not set up. |

**A/B swap one-liner** (Haiku ↔ any of the ✅ models):
```
TELNYX_API_KEY=$(grep MS_TELNYX_API_KEY apps/vap/nelson/.env.dev | cut -d= -f2) \
  python scripts/py_scripts/telnyx-manage.py update \
    --ai_assistant_id=assistant-3b0a64d1-f69e-4b22-b18c-1277e0e52699 \
    --set model=<MODEL_ID>
```

## Cost estimates (per 90s call)

Grounded in the research doc's model (1.5 min call, ~2.3K input tokens, ~0.2K output tokens, ~$0.003/call for amortized DID). Telnyx "rest of stack" = observed $0.16 baseline minus the Qwen3 LLM portion ≈ $0.158/call infra+STT+TTS+telephony. Swap-delta for each LLM is added to that base.

| Stack | $/call | $/1K calls | $/mo @ 3K | $/mo @ 10K |
|---|---|---|---|---|
| **Telnyx + Qwen3-235B (observed baseline)** | **$0.160** | $160.00 | $480.00 | $1,600.00 |
| **Telnyx + Claude Haiku 4.5 (set now)** | **$0.162** | $161.52 | $484.56 | $1,615.20 |
| Telnyx + Kimi-K2.5 | $0.160 | $160.10 | $480.30 | $1,601.00 |
| Telnyx + GPT-4.1 | $0.164 | $164.42 | $493.26 | $1,644.20 |
| Telnyx + GPT-4o | $0.166 | $165.97 | $497.91 | $1,659.70 |
| Telnyx + GPT-5 / 5.1 / 5.2 | $0.167 | $166.97 | $500.91 | $1,669.70 |
| ElevenLabs Agents + Sonnet 4.6 (LLM absorbed now) | $0.137 | $137.00 | $411.00 | $1,370.00 |
| ElevenLabs Agents + Sonnet 4.6 (worst case) | $0.147 | $147.00 | $441.00 | $1,470.00 |
| Vapi + GPT-4.1 + Nova-3 + Cartesia Sonic | $0.133 | $133.00 | $399.00 | $1,330.00 |
| Retell + Sonnet 4.5 + ElevenLabs | $0.285 | $285.00 | $855.00 | $2,850.00 |
| OpenAI Realtime API (~$1/min) | $1.500 | $1,500.00 | $4,500.00 | $15,000.00 |

**Impact of the Haiku swap alone**: +$0.002/call, **+$4.56/month** at 3K calls/mo, **+$15.20/month** at 10K calls/mo. Less than 1% cost delta.

**Migration payback** (assuming $10K dev cost for a platform migration — very rough, ~2 weeks of work):

| Migration | Savings vs current | Payback |
|---|---|---|
| ElevenLabs Agents (LLM absorbed) | $69/month @ 3K calls | **145 months** (~12 years) |
| ElevenLabs Agents (worst case) | $39/month @ 3K calls | **256 months** (~21 years) |
| Vapi premium stack | $81/month @ 3K calls | **123 months** (~10 years) |
| Retell | **NEGATIVE — costs $375/mo more** | Never |

**Verdict: migration for cost reasons is a non-starter at this volume.** Stay put.

## Open: side experiments worth trying

- [ ] A/B test `openai/gpt-4o` vs `anthropic/claude-haiku-4-5` on real calls — Haiku should win on rule following; worth confirming via the 4 probes on each.
- [ ] A/B test `openai/gpt-5` (or 5.1, 5.2) — Telnyx just added these. Not in the research doc. Might be cheap-enough-and-strong-enough to displace Haiku.
- [ ] A/B test `moonshotai/Kimi-K2.5` — lowest marginal cost on Telnyx. Unknown instruction-following quality. Long-shot dark horse.
- [ ] Try BYOK Sonnet 4.6 on Telnyx (requires adding an Anthropic API key in the assistant's `llm_api_key_ref`) if Haiku still fails the probes.
- [ ] File a Telnyx support ticket: surface `transcription.settings.keyterm` for Deepgram Flux on AI Assistants so automotive vocab boosting works without a Deepgram direct integration.

## Notes

- **Why the LLM matters more than the prompt**: the four rule violations are all classic "weak instruction follower" bugs. A strong model (Claude, GPT-4) with the OLD prompt probably would have followed the rules. A weak model with the NEW prompt may still fail. The prompt rewrite is the cheap first move; the durable fix is moving to a better LLM for this assistant. **Haiku 4.5 is now the durable fix.**
- **Telnyx PATCH responses are misleading** — when you PATCH `/ai/assistants/{id}` with a partial payload, the response body returns `None` for fields that weren't in the payload, even though the stored state is correct. Always GET to verify. Learned the hard way after an "assistant wiped" scare that turned out to be my code parsing `r.json().get("data", {})` against a flat (not `data`-wrapped) response.
- The control server uses a long-running APScheduler background job for hourly `LocationDB.refresh_all_locations`. If the laptop sleeps, you'll see "Run time of job ... was missed by ..." on wake — cosmetic.
- Python `print()` output from the control server is fully buffered when captured to a file via the background task runner. Flask/werkzeug logs flush per-request, but LocationDB load count messages don't appear until a buffer flushes. If you can't see the "loaded N locations" line, it doesn't mean the load didn't happen.
- `run_all_ngrok.py` originally had commented-out code that would auto-update the Telnyx voice app's webhook URL via `scripts/py_scripts/telnyx-manage.py`. Since the persistent ngrok URL is stable across restarts (same token → same alliterative subdomain), this rewiring isn't needed day to day.
- **The `/mcp` reverse proxy is a workaround, not a design choice.** It exists because ngrok's free tier reserves one domain per account and two `ngrok.forward()` calls both collapse onto it, silently clobbering each other. If you upgrade ngrok, you can have two tunnels and drop the proxy.
- **Secrets**: `apps/vap/nelson/.env.dev` contains `MS_TELNYX_API_KEY` and `NGROK_AUTHTOKEN` in plaintext. The file is `.gitignore`'d but keep it out of screenshots/pastes.
