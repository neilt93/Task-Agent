---
status: reference
created: 2026-04-14
---

# Nelson voice agent — bug-pass findings and config changes (2026-04-14)

> **Sendable summary** of today's work on `apps/vap/nelson`. Branch `fix-prompt-rule-following`, 9 commits ahead of `main`, not pushed. 98/98 tests passing.
> Focus: the assistant was violating explicit prompt rules on live calls ("Thanks, Neil" / "your appointment is all set" / "unknownvaplocation_name"). Root-caused, fixed, stress-tested, then did two follow-up bug passes finding more unrelated issues. Headline: there's a real production opt-out bug affecting at least one of our customer shops.

---

## TL;DR

1. **The original prompt-following bug is fixed.** Prompt rewrite plus LLM swap to `anthropic/claude-haiku-4-5` passed a 16-probe stress battery and a synthetic tool-call battery. Model verified to actually *call* `make_booking` / `take_message` (not just say it did), with correct `call_id`.
2. **Found and fixed a real customer-impact bug**: `voice_ai_enabled=False` on the location document was being **completely ignored** by the control server. At least three nelson locations have the flag set (including one non-test shop, `nelsons_dinkytown`). Those customers opted out of the voice AI answering their line, but our code answered anyway.
3. **Found 8 other real bugs** in the call pipeline, MCP server, and Flask proxy. All fixed on the branch.
4. **Config changes applied to `neil-nelson-001` only** (no touching of team assistants): swapped LLM to Claude Haiku 4.5, added GPT-4.1 as fallback, switched voice to the Telnyx Ultra "Amber - Warm Support Agent" that `new-default-sobti-nelson-001-no-mcp` already uses, enabled `rnnoise` noise suppression, attached the MCP server with the `make_booking` / `take_message` tools, reduced `time_limit_secs` from 1800 → 600, set `user_idle_timeout_secs` from null → 30.
5. **Three items need team decisions** before deploy: call-recording disclosure, MCP auth header format, fleet-wide adoption of the neil-nelson-001 config changes.

---

## Bugs fixed on branch `fix-prompt-rule-following`

Commit-level detail in `git log`. Short version:

| # | File | Bug | Commit |
|---|---|---|---|
| 1 | `control/main.py` | **`voice_ai_enabled=False` customer opt-out was ignored.** `grep voice_ai_enabled` on the codebase returned zero matches. `nelsons_dinkytown`, `test_sobti_auto_repair`, `test_sobti_restaurant` all have the flag set and were being answered anyway. Defense-in-depth checks added in `process_call_initiated` and `process_call_answered`. | `f688594e` |
| 2 | `control/main.py` | **`CallDB.initialize_call` wasn't idempotent on Telnyx retries.** Every time Telnyx redelivered a `call.initiated` webhook, the call state dict was reset with `answered: False`, defeating the "do once" idempotence checks in the downstream handlers (the ones added in `d572f5ba`). Fixed with a double-checked lock pattern. | `eb185654` |
| 3 | `control/main.py` | **`process_call_conversation_ended` asserted `call is not None`**, which becomes a Flask 500 → Telnyx webhook retry → infinite loop. Happens naturally after the 5-min GC fires, after a server restart, or for events arriving for a call we never saw initiated. Replaced with log-and-return. | `eb185654` |
| 4 | `_unused_mcp/main.py` | **`fire_and_forget` kept no strong reference to its `asyncio.create_task` returns.** Python docs: the event loop only holds WEAK refs to tasks. GC can reclaim the task mid-execution, silently losing the Firestore dump or control-server POST. Fixed with a module-level set + done callback. Also hardened `make_event` against malformed input. | `eb185654` |
| 5 | `control/model_prompts.py` | **Date resolution: weekday-name-only requests like "Friday" were being resolved to April 18 (a Saturday) instead of April 17.** Haiku wasn't reading the rendered calendar and was falling back to pretrained 2026 day-of-week math, which is off. Rewrote `get_calendar()` to emit an explicit weekday-anchor table per day plus a `CRITICAL: use only this calendar` instruction. After the fix, 7/7 date scenarios pass. | `a8ef9708` |
| 6 | `control/model_prompts.py` | Two duplicate `Once you have all three` paragraphs in the booking flow — one said "summarize and close", the other said "call `make_booking` and close". Weak follower could satisfy the first and skip the tool call. Consolidated into a single numbered sequence. | `0fbe5be2` |
| 7 | `_unused_mcp/main.py` | MCP tool docstrings were programmer signatures (`"Given the call_id from the prompt (str), caller_name (string)..."`) rather than behavioral LLM-facing descriptions. Rewrote both to include the anti-booking framing ("Never say the appointment is booked, scheduled, confirmed, or all set") directly in the tool catalog so the model sees it even if it skims the system prompt. | `7958920f` |
| 8 | `control/main.py` | **`/mcp` Flask reverse-proxy had zero authentication.** Anyone knowing the public ngrok URL could POST `make_booking` and pollute Firestore. Added an optional `MS_MCP_SHARED_SECRET` env-var gate + filtered hop-by-hop + Authorization headers on the forward path. Matching `/ai/secrets` integration_secret + MCP server `api_key_ref` set on the Telnyx side. **Partial — unverified against a live Telnyx call.** See open items. | `49bac0b7` + `eb81f9f7` |
| 9 | `control/main.py` | `LocationDB.UNKNOWN_LOCATION` had placeholder strings (`"unknown_vap_location_name"`) that TTS literally read aloud when a caller dialed a number not in Firestore. Replaced with speakable fallbacks. | `a9c7ffc6` (original session) |

Plus the original prompt rewrite (commit `a9c7ffc6`): CRITICAL RULES at the top, rebook-as-request framing, minimal-input handling, trailing `## Never` reinforcement. Tests: 18 → 98, all passing.

---

## Config changes applied to the Telnyx assistant (not in git)

These are all direct PATCHes to `assistant-3b0a64d1-f69e-4b22-b18c-1277e0e52699` (`neil-nelson-001`). Other team assistants not touched. Surveyed before each change — all 11 nelson assistants were on Telnyx defaults for these fields.

| Field | Before | After | Why |
|---|---|---|---|
| `name` | `neil-nelson-001-no-mcp` | `neil-nelson-001` | Not "no mcp" anymore |
| `model` | `Qwen/Qwen3-235B-A22B` | `anthropic/claude-haiku-4-5` | 16/16 stress probes pass; Qwen3 thinking-mode empty-content reliability issue |
| `fallback_config.model` | `""` | `openai/gpt-4.1` | Free resilience, no one on the account has validated the field yet |
| `mcp_servers` | `[]` | `[{id: 069d43b4-…}]` (new `neil-nelson` MCP server record) | Real tools for `make_booking` / `take_message` |
| `voice_settings.voice` | `Telnyx.NaturalHD.astra` | `Telnyx.Ultra.a7a59115-…` ("Amber - Warm Support Agent") | `new-default-sobti-nelson-001-no-mcp` already uses this; explicitly tuned for customer service per the catalog label |
| `telephony_settings.noise_suppression` | `null` | `"rnnoise"` | Free, helps with phone/bluetooth/car noise contributing to STT errors |
| `telephony_settings.time_limit_secs` | `1800` | `600` | 10 min is 5× the typical 90s call; stops a frozen call eating 30 min of platform minutes |
| `telephony_settings.user_idle_timeout_secs` | `null` | `30` | Silent caller → end call after 30s |
| `transcription.api_key_ref` | `""` | (unchanged) | No BYOK needed; Flux stays Telnyx-hosted |

---

## Open items needing team decisions

### 1. MCP auth header format — unverified

We set `api_key_ref` on the Telnyx MCP server record and created a matching `/ai/secrets` integration_secret. The Flask `/mcp` route now checks `Authorization: Bearer <secret>` if `MS_MCP_SHARED_SECRET` is set. **What I couldn't verify without a phone call**: whether Telnyx actually forwards the stored bearer in that exact header and format. Turning the env var on without testing a live call could block Telnyx's own tool calls.

**Ask for the team**: when you next test a call, set `MS_MCP_SHARED_SECRET` in `.env.dev`, dial the number, and try to book an appointment. If the booking tool fails with 401, check the control-server log for the line `"/mcp rejecting unauthenticated request (Authorization=...)"` — the preview shows what Telnyx actually sent. Share it and we can update the check. If the booking succeeds, the auth path is validated fleet-wide.

### 2. Call-recording disclosure — compliance risk

`recording_settings.enabled=True` on all production nelson assistants, dual-channel mp3. The greeting template has no recording disclosure. Several US states require **all-party consent** for call recording (CA, FL, IL, MD, MA, MT, NH, PA, WA). If we onboard a real shop in any of those states, this is a legal exposure.

**Three possible approaches**:
1. Add a per-location `vap_recording_disclosure` string to the Firestore location doc. The greeting template prepends it if present, skips it if null. Gives each shop opt-in control.
2. Disable recording on assistants for shops in two-party-consent states.
3. Fleet-wide disclosure in the greeting for every shop.

I did NOT modify the shared greeting template because this is a business decision, not a bug fix. Flagging for the team to pick one.

### 3. Fleet-wide adoption of neil-nelson-001 config changes

The per-assistant PATCHes I applied (Haiku 4.5, GPT-4.1 fallback, Ultra voice, rnnoise, 600s limit, 30s idle, MCP) are isolated to `neil-nelson-001`. If the live-call test validates them, consider applying to sobti/nadeem/demo assistants. Recording + compliance disclosure would need to land alongside.

### 4. Telnyx support tickets to file

Three undocumented API gaps hit during research:

1. **Deepgram Flux `keyterm` field not exposed** on `transcription.settings`. Probed 8 field-name variants, all silently dropped on PATCH. Ask Telnyx to surface `keyterm` (up to 100 strings) for custom vocabulary boosting — this is the real fix for the "tires → ties" STT error we saw on the 2026-03-30 call.
2. **`external_llm` schema undocumented**. Every shape I tried returned `400 "A required parameter was missing"` with no hint about which parameter. Currently blocks BYOK for Anthropic Sonnet/Opus. Ask Telnyx for the exact required fields.
3. **MCP server `api_key_ref` runtime behavior**. Confirm what header Telnyx uses to forward the bearer token on outgoing MCP calls. Documented behavior would let us close item (1) above.

---

## What's in the branch + how to review

- Branch: `fix-prompt-rule-following` (local only, not pushed to the 4mainstreet org remote)
- Base: 9 commits ahead of `main`
- Files touched: `apps/vap/nelson/control/main.py`, `apps/vap/nelson/control/model_prompts.py`, `apps/vap/nelson/control/test_model_prompts.py`, `apps/vap/nelson/_unused_mcp/main.py`, `apps/vap/nelson/running_local/run_all_ngrok.py`
- Tests: 18 → 98, all passing. New tests cover: return shape, CRITICAL RULES presence, banned-phrase coverage, no leftover placeholders, calendar + anchor table, greeting template, UNKNOWN_LOCATION speakability, handling-section coverage, tool-call instructions, substitution edge cases.
- Not modified: no changes to any other app, no changes to shared packages, no git hook bypass, no force-push.

## Suggested review order

1. `apps/vap/nelson/control/model_prompts.py` — the prompt rewrite + calendar anchor table. Most of the real behavior change lives here.
2. `apps/vap/nelson/control/main.py` — `CallDB.initialize_call` double-checked lock, `voice_ai_enabled` opt-out checks, MCP reverse-proxy route + auth gate.
3. `apps/vap/nelson/_unused_mcp/main.py` — tool docstring rewrite, fire_and_forget task-ref fix, dead-code cleanup. (Also: probably time to `git mv _unused_mcp mcp` — it isn't unused anymore.)
4. `apps/vap/nelson/control/test_model_prompts.py` — test coverage expansion.
5. `apps/vap/nelson/running_local/run_all_ngrok.py` — single-tunnel revert with a comment explaining why.

## Pre-merge checklist

- [ ] Live call to `+1 612 493 4839` — run the 4 scripted probes (say "Hello", book a flat-tire appt, ask for Nick's email, ask for brake cost)
- [ ] Live call again with `MS_MCP_SHARED_SECRET` set — verify booking tool still fires (or unset on 401 and share the log line)
- [ ] Decide on recording disclosure strategy (1, 2, or 3 from the Open Items section)
- [ ] File the three Telnyx support tickets
- [ ] Decide whether to push to `origin/fix-prompt-rule-following` and open a PR
- [ ] Decide whether to apply the per-assistant config changes to sobti/nadeem/demo assistants

## Nothing touched

For the avoidance of doubt, nothing outside `apps/vap/nelson/` was modified. No changes to any other team member's Telnyx assistants, MCP server records, location documents, or Firestore data. No secrets committed to git. No force-pushes. No `git config` changes. The `4MainStreet/` folder in my personal Obsidian vault was intentionally not touched after my first attempt to put notes there was reverted.
