---
status: active
created: 2026-04-29
---

## Goal

Build the two-agent AutoLab research system:
- **Filter Agent** (M0-M7, shipped): daily arXiv triage with score = T_cal × M_cal, $30/month hard cap.
- **Ideator** (I0-I7, shipped 2026-04-29): bandit-driven research ideation on top of the Filter, hands off to AIDE for execution.
- **MLE-Bench smoke test** (I8, gated): end-to-end Spaceship Titanic run with real Kaggle data + AIDE.

## Tasks

### Done in this session
- [x] Full autolab_ideator package (I0-I7) built and tested; 210/210 tests passing
- [x] Migrations 0004-0007 (api_calls.session_tag, ideator_signatures, archive_index, outcomes)
- [x] AIDE submodule added at vendor/aideml; .aide_venv/ install path documented
- [x] router/ai_adapter.py rewritten to AIDE's real Hydra `key=value` contract
- [x] OpenRouter shim verified (OPENAI_BASE_URL=openrouter + anthropic/claude-* model)
- [x] End-to-end AIDE run on bundled house_prices: 3 steps, RMSE=0.13, real lightgbm code
- [x] **`make ideate-aide-smoke`** — full E2E on AIDE's bundled house_prices (no Kaggle)
- [x] Verified outcome lands in `ideator_outcomes` (idea 389b3c3a7143-001-1, score=0.1317)
- [x] CLAUDE.md updated with Ideator + AIDE setup section

### Open / Kaggle path (skipped — using open source instead)
- [ ] ~~MLE-Bench / Spaceship Titanic~~ — skipped per "use whatever is open source". `make ideate-mle-bench-smoke` still in tree but gated; the open-source `make ideate-aide-smoke` covers the same ground without Kaggle.

### Open / next session
- [ ] Wire AIDE outcome → bandit reward + archive Elo update inside `aide_smoke.py` (currently only `ai_run.py` has the `--update-bandit` / `--update-elo` flags)
- [ ] Decide whether to estimate AIDE's external spend and log it to `api_calls` for budget visibility (currently AIDE bypasses our spend tracking)
- [ ] Try `make ideate-aide-smoke` against `bitcoin_price` to confirm dataset-portability

## Milestones

| Target Date | Milestone | Status |
|---|---|---|
| 2026-03 | Filter M0-M7 | done |
| 2026-04-29 | Ideator I0-I7 + AIDE wiring on synthetic | done |
| 2026-04-29 | I8 — open-source E2E on house_prices (no Kaggle) | done |
| TBD (only if needed) | Optional Kaggle/MLE-Bench path | deferred |

## Log

- **2026-04-29** Ideator I0-I7 shipped. AIDE adapter rewritten to real Hydra contract after I overstated I7 in the prior session ("the Implementer didn't actually run"). Verified end-to-end on AIDE's bundled `house_prices` task: real Sonnet-4.6-via-OpenRouter call, real lightgbm code, RMSE=0.13. Commit `c8c2e8a`.
- **2026-04-29** Added `make ideate-aide-smoke` for open-source E2E (no Kaggle). Reused existing archive entry `389b3c3a7143-001-1` → handoff regen → AIDE 2 steps on house_prices → RMSE=0.1317 → outcome row in `ideator_outcomes`. 58s wall-clock, $0.003 internally tracked. Commit `ea32b59`.

## Notes

### AIDE wiring gotchas
- AIDE's `backend_openrouter.py` hardcodes Fireworks as the only provider — useless for Anthropic models. Workaround: route via OpenAI-compatible shim (`OPENAI_API_KEY=<openrouter>`, `OPENAI_BASE_URL=openrouter URL`, model name like `anthropic/claude-sonnet-4.6` so the dispatcher misses the `claude-*` prefix and falls through).
- AIDE prefixes `<index>-` to `exp_name` in the log_dir. Adapter globs for it.
- `vendor/aideml/requirements.txt` includes torch+lightgbm+xgboost as agent runtime deps. The `make aide-venv` target installs only AIDE itself + lightgbm+xgboost; torch is skipped to keep the venv ~200MB instead of ~3GB. Add torch back if AIDE-generated code needs it.

### Spend leak
AIDE's LLM calls bypass our `api_calls` table — our budget guards (`assert_under_ideator_cap`, `assert_under_combined_cap`) don't see them. Treat AIDE as a separate spend bucket; bound exposure with `agent.steps`.

### Commands
```sh
make submodules                  # git submodule update --init --recursive
make aide-venv                   # install AIDE in .aide_venv (one-time)
export IDEATOR_AIDE_BIN=$(pwd)/.aide_venv/bin/aide
make ai-run IDEA_ID=X HANDOFF_DIR=Y DATASET_DIR=Z  # invoke AIDE on a handoff
```
