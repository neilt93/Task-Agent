---
status: active
---

## Goal

LLM-powered crypto wallet with robust safety engine. Natural language interface for send/swap/batch/chain/stream/bridge on Base. Eval suite to validate safety against adversarial attacks.

## Tasks
- [x] V2 core: 11 intent types, relative amounts, batch/chain, streaming, bridging
- [x] Safety engine: 4-check pipeline (simulation, approval, reputation, semantic) + external check
- [x] NEAR Intents: Skills API, policy layer, bridge adapter
- [x] Eval suite: 138 test cases (intent, safety, e2e)
- [x] Adversarial safety eval: 32 test cases across 6 attack categories
- [x] Decision trace logging in safety engine (per-check timing + audit trail)
- [x] Safety metrics reporter (catch rate, false negatives, per-category/severity)
- [x] Policy engine audit logging + denial cooldown
- [x] Skills API hardening (input validation, size limits, sanitized errors, security headers)
- [x] Threat model doc + failure case demos
- [ ] Run full adversarial eval against live server and tune thresholds
- [ ] Address book verification for new recipients
- [ ] Per-session spending limits
- [ ] Real-time transaction monitoring / alerting

## Notes

- 170 total eval cases: 112 intent + 10 safety + 16 e2e + 32 adversarial
- Adversarial categories: prompt-injection (8), address-poisoning (5), approval-exploit (5), social-engineering (5), amount-manipulation (5), phishing (4)
- Port 3003 (3000 occupied by NeilSearch)
- Run eval: `EVAL_API_URL=http://localhost:3003 npm run eval`
- Dry-run: `npx tsx eval/run.ts --suite adversarial --dry-run`
