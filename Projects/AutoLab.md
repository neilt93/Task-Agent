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
- [x] ~~Wire AIDE outcome → bandit reward + archive Elo update inside `aide_smoke.py`~~ — done 2026-05-02
- [ ] Decide whether to estimate AIDE's external spend and log it to `api_calls` for budget visibility (currently AIDE bypasses our spend tracking)
- [ ] Run the Filter daily for 2-4 weeks to grow the corpus from 250 → ~1200 papers (architecture's design point for k=24 clustering)
- [ ] Add cs.CL and q-bio.QM to the Filter's arxiv ingest categories (currently cs.LG / cs.AI / stat.ML only)
- [ ] Once corpus is saturated: re-mine arms at k=24 and verify cluster summaries become thematically coherent
- [x] ~~Then: 50-vs-50 closed-loop-vs-frozen-bandit experiment (validates whether the architecture beats baseline)~~ — done as v4 Phases 3–7 (closed vs frozen, pre-registered, up to 60+60). **Result: 3× FAIL_NULL + 1 PARTIAL — closed-loop bandit shows no statistical advantage.** A robust pre-registered negative; bandit machinery itself is sound (n=200 positive control PASSes; the n=30 control's FAIL was a calibration artifact, not a bug).
- [ ] Autoresearcher next steps (deferred, not green-lit): executor-regeneration variance + 3rd held-out clip → component ablation → optional NeuroMechFly-v2 reimplementation for an external anchor
- [ ] W1 RECAP code (subagent/recap.py + budgets.py) — context discipline; deferred since the audit found no contamination violations, this is hygiene/scalability for long sessions

## Milestones

| Target Date | Milestone | Status |
|---|---|---|
| 2026-03 | Filter M0-M7 | done |
| 2026-04-29 | Ideator I0-I7 + AIDE wiring on synthetic | done |
| 2026-04-29 | I8 — open-source E2E on house_prices (no Kaggle) | done |
| 2026-05-03 | v2: override-path fix + bounded-compute prompts + loop closure, 3 real benchmarks | done |
| 2026-05-09 | v4 closed-loop validation (Phases 3–7, pre-registered) | done — 3× FAIL_NULL + 1 PARTIAL (robust negative) |
| 2026-05-15 | v5 planner-executor separation (Phases A–B) | done — both PASS |
| 2026-05-15 | Minimum-viable autoresearcher + baseline/generalization validation | done — +42% vs best baseline (target-1), +14% (target-2, qualified) |
| 2026-05-15 | Presentation package (summary + figure + Soubhik draft) | done |
| TBD (only if needed) | Optional Kaggle/MLE-Bench path | deferred |

## Log

- **2026-05-16 (Phase δ — variance) ROBUST** Surfaced+resolved with user that β has NO seed (deterministic t=0; Anthropic-via-OpenRouter forwards none) → δ = user-approved variant: full β setup ×3 independent **stochastic** reps at **t=0.5** (not seeded; pre-reg `913e086`; additive temperature param byte-verified; @v1/@v2 untouched; β/γ/coverage runners unmodified). **All 3 reps → PARTIAL** (same C1✓ C2✗ C3✓ structure as original β); best-DTW [0.0289,0.0355,0.0291] spread 0.0067, all > 0.0246 γ-bar, none learned (p≥0.16) → locked 3-category meta = **ROBUST**. The β-PARTIAL/γ-plateau finding is **NOT sampling noise** — the load-bearing n=1 caveat is RESOLVED; the only un-pre-registered reviewer objection is now pre-registered + answered. Folded into aggregation/summary/soubhik. Durable +42%-vs-baseline unchanged. Residual (optional hardening, not prerequisites): n=3 small, single target, no external comparison. Commits `14f8766`,`22c43dc`. Regression 266/3. $7.88/$10.

- **2026-05-16 (coverage-prompting variant)** Pre-registered (`f0b24b0`) anti-pattern test of the natural objection to the γ negative — show planner explored-vs-uncovered (arch,obj) coverage, not winners (NEW `@v3`; `@v2`/v1 byte-unchanged; β/γ runner untouched; additive planner verified). 5×8=40. **Verdict PASS by the locked one-of-two rule but C2-ONLY**: C1 (break plateau, best<0.0246) **FAIL** — best 0.0346, *worse* than γ 0.0259; C2 (≥3 archs top-20) PASS — 7 archs (explored as told). **Substantive negative: rules OUT proposer-pattern-matching as the plateau cause → the γ plateau is a genuine downstream ceiling (executor/metric/task), not planner space-coverage. Hardens the iteration negative (natural fix pre-registered + failed).** Folded into aggregation/summary/soubhik. Commits `d1d5fe4`,`552b867`. Regression 266/3. $2.34/$10.

- **2026-05-16 (Phase γ COMPLETE)** Credits restored → deterministic resume of γ rounds 5–9 (after one more transient: a post-run `relative_to` crash *after* all 10 rounds saved — science intact, verdict computed post-hoc per the pre-committed blind amendment, runner byte-frozen). **γ FAIL_NULL (1/3 amended criteria)**: C1 best 0.02592 > 0.0239 FAIL; C2 no-downtrend FAIL (positive slope even excluding the round-13 divergent-plan outlier — outlier dramatized, didn't cause); C3 < β-best PASS by ~2.4%. **Headline (stronger than the verdict): global best 0.02592 hit at γ round 0 and never beaten in 9 further rounds — iteration does not compound; ≈ a better one-shot draw conditioned on history.** No diversity collapse (8/8 every round). Agent converged in design space → transformer/predictive_coding/same_fly_different_clip (gen-target 20/20 of top plans). Aggregation `experiments/v5_iteration_summary_report.md` written; `autoresearcher_summary.md` (+Result 4) and `soubhik_update.md` updated (sharpened, not softened; editable, not sent). Durable positive unchanged: +42% vs best manual baseline. Limitation: n=1 deterministic — Phase δ (variance) is the load-bearing next step. Commits `8a33aeb`, `1596238`. Regression 266/3.
- **2026-05-16 (refactor & cleanup pass, γ blocked)** Pre-registered (`a674c76`) refactor/cleanup with byte-freeze invariant for the γ-resume-critical set. Phase 1 inventory (`research/repo_inventory.md`, `4e574f0`) → Phases 2–6 (`7611dec`): **0 code/byte changes by design** — eligible surface near-empty (all material code frozen/pre-reg/production; first-party already clean; the 9,589 `ruff` headline is ~99% immutable experiment/vendor scratch). Deliverables: `refactor_findings.md` (F1–F4 flagged-not-fixed w/ rationale; no result-affecting bug — the lone F821 is a never-evaluated string annotation, v4 verdicts valid), `refactor_cleanup_report.md`, README→REPO_MAP pointer. Test hygiene verified (planted-leak detection 8/8, determinism 266/3 stable). Regression green every commit; byte-freeze set untouched (empty diff). Honest outcome: restraint + documentation, not churn.
- **2026-05-16 (idle cleanup, γ blocked)** Safe repo cleanup while γ waits on credits: removed 4 stale v4 `*.tmp` scratch dirs + 2 superseded smoke dirs; hardened `.gitignore` (`*.tmp/`); **wrote the long-outstanding v4 Phase 7 `REPORT.md`** (FAIL_NULL n=60+60, figures verbatim from committed SUMMARY) + committed the phase7b1 run dir; added top-level **`REPO_MAP.md`** (arc + locked-surface map + γ resume command + pre-reg discipline). Deliberately **did not** refactor any locked/γ-critical/production code (v4, v5 interfaces/scoring/planner/executor, runner, prompts, filter, ideator, experiments) — deeper refactor deferred to post-γ because γ resume requires byte-stability of those. Commits `bf55127`, `9635ea1`.
- **2026-05-16** Phase γ (convergence, 10-round continuation) **BLOCKED — INCOMPLETE_INFRA**: OpenRouter API key out of credits (402, "can only afford 513 tokens") at γ round 5/10. Not transient — external billing limit. 5/10 rounds atomically saved (resumable; γ-so-far best 0.0259, but **no verdict** — criterion-2 back-half is entirely in the unrun rounds; anti-gaming forbids a partial verdict). Aggregation report + summary/soubhik γ-verdict edits **not written** (gated on a verdict that doesn't exist — writing them would fabricate). γ pre-reg `dfd76e2` + pre-execution amendment `c495fb5` (criterion 1 → Best-DTW(γ) ≤ 0.0239 vs β-end 0.0266; committed BLIND before any γ result) are locked → resumed verdict stays a valid pre-registered test. **Needs: add OpenRouter credits, then resume command in `experiments/v5_phaseGamma_convergence_run_20260516T005515Z/DIAGNOSTIC.md`.**
- **2026-05-15 (later)** Iteration-validation. Phase α (iteration *mechanism* — `@v2` score-conditioned planner) PASS: 6/8 novel plans, learned toward the winning region. **Phase β (does iteration *cause* improvement)**: pre-registered (NoIter run once — t=0 determinism makes 5× degenerate; eval target = executor-locked `forward_walk_002_long`), 5 Iter rounds ×8 + NoIter ×8. **Verdict PARTIAL (2/3)**: C1 improvement PASS (best Iter 0.0266 ≤ 0.85×best NoIter 0.0371), C3 beat-one-shot PASS (0.0266 < 0.0388), **C2 learning FAIL** (round-mean slope +0.0008, p=0.85 — finds a better *winner* but does not lower the *mean*; honest narrower claim). Transient network error mid-run → runner-only resilience patches (retry + `--resume-dir` + cost-query fix), deterministic resume from atomic checkpoints (v4-Phase-5 disclosure precedent). True spend $3.01/$40 (post-hoc from `api_calls`; in-run meter mis-reported due to asyncpg str-vs-datetime, fixed). Regression 266 passed. **Stopped after β REPORT per user scope — Phase γ NOT started.** Discipline held: locked verdict not adjusted, pre-reg/thresholds/locked-code untouched.

- **2026-04-29** Ideator I0-I7 shipped. AIDE adapter rewritten to real Hydra contract after I overstated I7 in the prior session ("the Implementer didn't actually run"). Verified end-to-end on AIDE's bundled `house_prices` task: real Sonnet-4.6-via-OpenRouter call, real lightgbm code, RMSE=0.13. Commit `c8c2e8a`.
- **2026-04-29** Added `make ideate-aide-smoke` for open-source E2E (no Kaggle). Reused existing archive entry `389b3c3a7143-001-1` → handoff regen → AIDE 2 steps on house_prices → RMSE=0.1317 → outcome row in `ideator_outcomes`. 58s wall-clock, $0.003 internally tracked. Commit `ea32b59`.
- **2026-05-15** Big arc. (1) **v4 closed-loop validation closed out**: Phases 3–7 (closed vs frozen bandit, pre-registered, harder target, up to 60+60) → three FAIL_NULL + one PARTIAL. Closed-loop bandit does *not* beat the frozen control on VNC walking — a robust, pre-registered negative across 3 architectural variants. (2) **Bandit-machinery audit**: Phase 6 positive control FAILed at n=30; read-only MC diagnostic root-caused it as pre-reg bar over-aspiration (not a code bug — 80/80 convergence at n≥60); independent pre-registered n=200 control PASSes. The negative result is real, not an artifact. (3) **v5 planner-executor separation** built (`src/autolab_v5/`): Phase A interface lock (schemas + isolated scoring + transitive isolation test) PASS; Phase B planner (VS, 4-axis schema, target-supervision objectives removed) PASS, 8/10 valid plans, $0.04. (4) **Pivot to minimum-viable autoresearcher** (user reframed: "show the agent doing research, produce a concrete winner"): linear problem→library→propose→write code→run→DTW→pick. Winner plan-05 (transformer / generalization_across_clips / sensor_stream). (5) **Validated**: pre-registered vs Phase 0 manual baselines — plan-05 0.0388 beats best baseline C 0.0671 by **+42%** on a held-out clip, literal apples-to-apples (byte-identical training clip). Generalization test on a 2nd clip: still beats C (+14%) but *qualified* — absolute fidelity degrades ~3×, verdict metric-sensitive (flips under direct DTW). No external SOTA anchor exists (NMF v2 behavioral, Lappalainen is vision) — documented honestly. (6) **Presentation package** committed: `research/autoresearcher_summary.md`, `research/autoresearcher_validation.png`, `research/soubhik_update.md` (editable draft, not sent). Total agent spend ≈ $0.6. Discipline held throughout: pre-registration before every comparison, frozen designs, unmodified baselines, locked verdicts never overturned.
- **2026-05-02 → 05-03** Three architectural fixes after v3-PDF + advisor review: (1) `_read_handoff_strings` now compiles the full handoff bundle (decision tree branches, assumptions) into AIDE's goal string — verified via legacy-vs-fixed A/B that AIDE switched libraries (lightgbm→xgboost) and metrics (RMSE→adversarial-perturbation eval) on the same idea; (2) prompts/verbalized_sampling@v2 + grill_me@v2 with bounded-compute constraints (≤30 min CPU, sklearn/lightgbm/xgboost only, no GPU/distributed/pretraining); (3) bandit feedback loop closed in `aide_smoke.py` (Beta posteriors session-scoped, Elo deltas persistent). Plus novelty signal mixed into bandit reward via `score/novelty.py`. Three real benchmarks now in tree (`benchmarks/house_prices`, `tdc_admet_caco2`, `mmlu_pro_slice`) — verified loop closure on all three. Best result: TDC ADMET Caco2 MAE 0.3249 inside leaderboard SOTA range (0.27-0.34). v4 PDF at `reports/AutoLab-Ideator-v4-3benchmarks.pdf`.

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
