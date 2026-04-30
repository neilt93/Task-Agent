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
- [x] CLAUDE.md updated with Ideator + AIDE setup section

### Open / gated on me
- [ ] Install `git-lfs` via brew so `vendor/mle-bench/` can be cloned
- [ ] Drop a Kaggle API token at `~/.kaggle/kaggle.json` and accept Spaceship Titanic rules at kaggle.com
- [ ] Decide spend cap for the MLE-Bench smoke (steps=2 ≈ $1-3; steps=20 ≈ $10-20)

### Open / next session
- [ ] `git submodule add https://github.com/openai/mle-bench vendor/mle-bench` (after git-lfs)
- [ ] Run `make ideate-mle-bench-smoke COMPETITION=spaceship-titanic` end-to-end
- [ ] Verify score lands in `ideator_outcomes.metrics`
- [ ] Wire AIDE outcome → bandit reward + archive Elo update via `make ai-run --update-bandit --update-elo`
- [ ] Decide whether to estimate AIDE's external spend and log it to `api_calls` for budget visibility

## Milestones

| Target Date | Milestone | Status |
|---|---|---|
| 2026-03 | Filter M0-M7 | done |
| 2026-04-29 | Ideator I0-I7 + AIDE wiring on synthetic | done |
| TBD (gated on Kaggle creds) | I8 — Spaceship Titanic E2E with real data | blocked |

## Log

- **2026-04-29** Ideator I0-I7 shipped. AIDE adapter rewritten to real Hydra contract after I overstated I7 in the prior session ("the Implementer didn't actually run"). Verified end-to-end on AIDE's bundled `house_prices` task: real Sonnet-4.6-via-OpenRouter call, real lightgbm code, RMSE=0.13. Commit `c8c2e8a`.

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
