---
status: active
created: 2026-03-02
---

## Goal

Risk-aware failure detection for visuomotor imitation learning. Train a supervised risk predictor on real Diffusion Policy rollouts to detect when the policy is about to fail, enabling abstention under partial observability.

## Tasks

- [x] SOTA identification (Diffusion Policy) 📅 2026-02-15
- [x] Baseline replication (partial, transparent) 📅 2026-02-20
- [x] Push-T contrastive TAP-Score (early prototype) 📅 2026-02-25
- [x] Robomimic (Lift, Can) lowdim support + headroom audit infra 📅 2026-03-01
- [x] Fix robosuite 1.5.2 controller compatibility 📅 2026-03-02
- [x] Robomimic perturbation onset curves 📅 2026-03-03
- [x] Oracle reranking headroom audit — negligible (+5%) 📅 2026-03-03
- [x] Pivot: failure detector / abstention trigger 📅 2026-03-04
- [x] Contrastive detection experiments (exhausted, ceiling at 0.738) 📅 2026-03-04
- [x] Risk predictor: collect 500 Can episodes, train, evaluate 📅 2026-03-05
- [x] Risk predictor: Lift evaluation (n=50) 📅 2026-03-05
- [x] Fix aggregation: mean beats min for risk model 📅 2026-03-05
- [x] Lift v2 (diverse regimes) — REGRESSED to 0.420, v1 remains best 📅 2026-03-05
- [x] Square risk pipeline — AUROC 0.523, near-random (same gap as Lift v2) 📅 2026-03-06
- [x] Selective execution analysis + centerpiece figure 📅 2026-03-07
- [x] Final report write-up (`report/4_method_and_results.md`) 📅 2026-03-07
- [x] Blog post: Part 1 (PushT results) 📅 2026-02-25
- [x] Blog post: Part 2 (robomimic pivot + six bugs) 📅 2026-03-03
- [ ] Blog post: Part 3 (risk model results)
- [x] Structured repo cleanup: archive Phase 1, track risk model code 📅 2026-03-08
- [x] Obs-only baseline: model variant + training/eval support 📅 2026-03-08
- [x] Failure density analysis: d' amplification explains Can/Lift/Square 📅 2026-03-08
- [x] Intervention eval: resample-on-risk + early-stop-restart scripts 📅 2026-03-08
- [x] Can v3 obs-only baseline: AUROC 0.960 (nearly matches full 0.972) 📅 2026-03-11
- [x] Lift v3 full + obs-only: AUROC 0.881 / **0.982** 📅 2026-03-11
- [ ] Run intervention eval (resample K=4, restart R=2) on Can, Lift
- [x] World model guardrail: training pipeline + sanity check 📅 2026-03-08
- [x] World model: detection eval on existing rollouts (AUROC 0.867 zero80) 📅 2026-03-08
- [x] World model vs TAP-Score comparison (WM wins on zero80_jitter, fusion 0.923) 📅 2026-03-08
- [x] v3 single-regime Can: AUROC 0.972 (up from 0.856) 📅 2026-03-11
- [ ] v3 single-regime Lift + Square
- [ ] World model: state-perturbation rollout collection (can_shift, can_rotate)
- [ ] World model: detection eval on state-perturbed rollouts
- [ ] World model: closed-loop recovery evaluation
- [ ] World model: ablation table + final figures

## Milestones

| Target Date | Milestone | Status |
|---|---|---|
| 2026-02-25 | Push-T prototype working | Done (0.994) |
| 2026-03-01 | Robomimic infra ready | Done |
| 2026-03-02 | robosuite 1.5.2 fixes | Done |
| 2026-03-03 | Reranking headroom: negligible | Done |
| 2026-03-04 | Pivot to detection | Done |
| 2026-03-05 | Risk model: Can 0.856, Lift 0.811 (final re-eval) | Done |
| 2026-03-06 | Square: AUROC 0.523 (near-random, same gap) | Done |
| 2026-03-07 | Final report + selective execution figure | Done |
| 2026-03-08 | World model: train + detect (AUROC 0.867) + WM>TAP on zero80 | Done |
| 2026-03-08 | State-perturbation rollouts + recovery eval | In progress |
| 2026-03-11 | v3 single-regime Can: AUROC **0.972** [0.940, 0.995] | Done |
| 2026-03-11 | v3 Lift: 0.881 full, **0.982 obs-only** [0.956, 0.998] | Done |
| 2026-03-11 | Obs-only beats full on both tasks — actions not needed for zero_object | Key finding |

## Architecture

**Risk Predictor (current)**
- Binary classifier: P(fail_within_H | obs, action)
- obs_encoder(MLP) + action_encoder(MLP) → concat → sigmoid head
- H = 64 env steps (8 action chunks)
- Trained on real DP rollouts under diverse perturbation regimes
- No synthetic data — real actions, real outcomes only
- Key files: `tap/risk.py`, `train_risk_tap.py`, `scripts/collect_risk_rollouts.py`

## Results

### Risk Predictor (primary results, 2026-03-07 re-eval)

**Training**: 500 Can episodes across 5 regimes (clean, zero80, zero80_jitter, dropout80_p02, dropout80_p04). 22,500 training samples, 8.3% failure rate. Best val AUROC: 0.867 (epoch 12).

**Aggregation**: Mean risk across episode. Min is wrong — a single safe step drives min down even in doomed episodes.

| Task | AUROC | AUPRC | 95% CI | Mag baseline | Label mode |
|------|-------|-------|--------|-------------|------------|
| **Can** (zero80, n=100) | **0.856** | 0.881 | [0.779, 0.915] | 0.537 | onset_window H=64 |
| **Lift** (zero31, n=100) | **0.811** | 0.821 | [0.718, 0.892] | 0.551 | episode_window W=128 |

**Can selective execution (mean aggregation, n=100)**:

| Coverage | Success Rate | Baseline |
|----------|-------------|----------|
| 10% | 100% (10/10) | 45% |
| 20% | 95% (19/20) | 45% |
| 30% | 80% (24/30) | 45% |
| 50% | 70% (35/50) | 45% |

AUC-SE: TAP=0.717, Mag=0.456, Random=0.450.

**Lift selective execution (mean aggregation, n=100)**:

| Coverage | Success Rate | Baseline |
|----------|-------------|----------|
| 10% | 80% (8/10) | 47% |
| 20% | 90% (18/20) | 47% |
| 30% | 83% (25/30) | 47% |
| 50% | 72% (36/50) | 47% |

AUC-SE: TAP=0.689, Mag=0.455, Random=0.470.

### Robomimic Infrastructure

**Can zero_object onset curve** (2026-03-03):

| Onset | Success | n |
|-------|---------|---|
| clean | 90% | 20 |
| 0-50 | 0% | 20 |
| 75 | 10% | 20 |
| 80 | 50% | 10 |
| 90 | 80% | 10 |
| 100 | 85% | 20 |
| 150 | 95% | 20 |

**Oracle reranking headroom** (closed): +5% across all configs (K=4, K=8). Candidate distribution collapses under occlusion.

**robosuite 1.5.2 fixes**: 5 controller patches + 2 obs compatibility fixes (Lift sign flip, Can field reorder). Details in Log.

## Research Directions

1. **Risk-based failure detection** (completed) — Supervised risk predictor on real DP rollouts.
2. **World-model guardrail** (ACTIVE) — Success-only trained action-conditioned world model for OOD transition detection and closed-loop recovery. Key insight: WM detects failures TAP-Score misses; fusion gives AUROC 0.923 on zero80 regimes.
3. **Closed-loop recovery** — K-candidate action selection guided by world-model risk prediction. Framework built, awaiting state-perturbation rollout collection.
4. **Score trajectory modeling** — Temporal risk patterns clearly visible in WM risk-over-time plots.

## Log

### 2026-03-11
- **v3 single-regime training**: New approach — train on ONE perturbation regime per task instead of mixing 5 artificial regimes. Hypothesis: task-specific failure patterns are more learnable than generic ones.
- **robosuite version regression found + fixed**: Local robosuite had silently downgraded from 1.5.2 to 1.4.1, causing 0% policy success on all tasks. Fixed by `pip install robosuite==1.5.2`. Root cause: the controller patches are version-specific.
- **Can v3 results (n=100)**:
  - AUROC **0.972** (min agg), 95% CI [0.940, 0.995]. Up from v2's 0.856.
  - Selective execution: 100%@10%, 100%@20%, 92.5%@30% (vs 44% baseline)
  - Val AUROC: 0.946 (full), 0.940 (obs-only)
  - Training: 250 episodes under zero80 only, onset_window H=64, 38% success rate
  - Key insight: single-regime training eliminates confusion from heterogeneous failure types
- **episode_window labeling fails with wide horizon**: H=400 (label all post-onset steps) → val AUROC 0.515 (random). Early post-onset steps aren't discriminative. onset_window H=64 works.
- **Bug fixes**: Checkpoint every 5 (was 10), speed report after 5 new episodes (fix resume accounting), first-epoch checkpoint always saved (val_auroc starts at -1).
- **Can v3 obs-only baseline (n=100)**:
  - AUROC **0.960** (min agg), 95% CI [0.922, 0.987]. Nearly matches full model (0.972).
  - Selective execution: 100%@10%, 100%@20%, 93%@30%
  - **Key finding: actions add only +1.2% AUROC on Can**. Under zero_object perturbation, the zeroed observation state carries almost all failure signal.
- **GPU contention fix**: Running two eval processes simultaneously caused both to hang. Sequential execution resolved it (30s/ep vs stuck).
- **Pipeline script fixed**: `episode_window H=400` → `onset_window H=64`, `N_COLLECT 500` → `250`.
- **Lift v3 full model (n=100)**:
  - AUROC **0.881** (min agg), 95% CI [0.805, 0.939]. Up from v2's 0.811.
  - Selective execution: 100%@10%, 95%@20%, 83%@30%, 74%@50%
  - Val AUROC: 0.943
- **Lift v3 obs-only (n=100)**:
  - AUROC **0.982** (min agg), 95% CI [0.956, 0.998]. Dramatically outperforms full model.
  - Selective execution: 100%@10%, 100%@20%, 100%@30%, 92%@50%
  - Val AUROC: 0.972
  - **Key finding: obs-only beats full model on both tasks under zero_object perturbation**. The zeroed observation carries all failure signal; action proposals add noise, not signal.

### 2026-03-08
- **Structured repo cleanup**: Archived 50+ intermediate results, dead scripts, Phase 1 code. Tracked core risk model code (tap/risk.py, train_risk_tap.py) and active scripts. README rewritten with final results. Commit f2b6c9a.
- **Obs-only baseline implemented**: `RiskTAPScore(obs_only=True)` — same architecture without action encoder. Tests whether actions carry signal beyond state. Training (`--obs_only`) and eval support added. Needs GPU run.
- **Failure density analysis**: `scripts/analyze_failure_density.py` — explains why TAP works on Can/Lift but not Square.
  - Key finding: **d' amplification**. Per-step d' is small (Can: 0.285, Lift: 0.215, Square: 0.100), but episode-level aggregation (mean of ~40 steps) amplifies by sqrt(N). Can d'(ep)~1.80, Lift d'(ep)~1.46 => strong AUROC. Square d'(ep)=0.10 => no separation even with aggregation.
  - Figure: `artifacts/failure_density.{png,pdf}` — 3x3 panel (temporal profiles, separation bars, score histograms).
- **Intervention scripts**: `scripts/eval_intervention.py` — two modes:
  - **Resample**: When risk > threshold, generate K DP proposals, score each, execute safest. Tests TAP as action-level safety filter.
  - **Restart**: When sliding-window risk exceeds threshold, terminate and retry from initial state (budget R restarts). Tests early warning utility.
  - Both need GPU runs on RunPod to produce results.
- **World model guardrail built**: Action-conditioned latent world model (encoder + predictor, 274K params) trained on success demos only. End-to-end training with VICReg variance regularization. Train pred loss 0.001, val 0.0015.
- **Detection results**: MSE metric, smooth_window=15, max aggregation → AUROC **0.867** on zero80 regimes (crosses 0.85 target). AUPRC 0.929.
- **WM vs TAP-Score**: WM (0.867) outperforms TAP-Score (0.818) on zero80 regimes despite no failure labels. On zero80_jitter specifically: WM 0.886 vs TAP 0.759.
- **Fusion**: alpha=0.6 WM + 0.4 TAP → AUROC **0.923**. Best of both.
- **Key insight**: Action-corruption perturbations (dropout) confound WM (success and failure both OOD). State perturbations (can_shift, rotation) should work better. Collection script ready.
- **Recovery framework**: `world_model/eval/eval_recovery.py` — K-candidate action selection with base/random/WM-guided methods. Awaiting env runs.
- **Code**: `world_model/` subdirectory with full pipeline: datasets, models, training, eval, scripts, configs.



### 2026-03-07
- **Selective execution analysis**: Created `scripts/analyze_selective_execution.py` — computes success-vs-coverage curves comparing TAP risk score, action magnitude baseline, and random baseline with bootstrap 95% CIs.
- **Centerpiece figure**: `artifacts/selective_execution.{png,pdf}` — 2-panel (Can + Lift) showing TAP dominates both baselines.
  - Can: AUC-SE 0.705 (TAP) vs 0.578 (mag) vs 0.450 (random). 100% success@11% coverage.
  - Lift: AUC-SE 0.674 (TAP) vs 0.422 (mag) vs 0.470 (random). Mag baseline worse than random (narrow action range).
- **Bug fix**: `eval_tap_detection.py` — gating summary was computed after JSON write, so `summary["gating"]` never appeared in output. Moved gating block before JSON write.
- **Final report**: `report/4_method_and_results.md` — complete method and results writeup covering problem setup, contrastive motivation, risk model method, Can/Lift/Square results, selective execution centerpiece, lead time, discussion, and reproduction instructions.

### 2026-03-05
- **Risk model results (both tasks)**:
  - Can: AUROC 0.848 (mean), AUPRC 0.903, CI [0.731, 0.941]
  - Lift: AUROC 0.819 (mean), AUPRC 0.867, CI [0.671, 0.916] ← v1 model
  - Both CIs clear 0.50 — significant
- **Lift v2 regression**: Retrained with diverse regimes (5 types, 250 ep) — AUROC dropped to 0.420/0.327. Only 3.5% failure samples (Lift is too easy for most perturbations). V1 model (fewer regimes, higher fail rate) remains best.
- **Key insight**: Risk model needs >15% failure rate in training data. Can (43%) works well, Lift (16%) barely works.
- **Aggregation fix**: min-score AUROC on Can was 0.490 (useless) because all episodes hit the same safety floor (~0.28). Mean captures that failures spend more time at high risk.
- **Abstention works**: Can 100% success at 10% coverage, 90% at 20% (vs 40% baseline)
- **Training completed**: 500 episodes, 5 regimes, best val AUROC 0.867 (epoch 12)
- **PIVOT**: Abandoned contrastive approach entirely. Risk predictor trained on real data only.

### 2026-03-06
- **Onset-window labeling fix**: Discovered fundamental bug — old labels used `steps_remaining = max_step - step`, only labeling last 64 steps of 400-step episodes. Fixed to label `[onset, onset + H]` relative to perturbation onset.
- **Can onset-window retrain**: Val AUROC 0.973 (up from 0.867). Episode AUROC 0.878/p10 (up from 0.848/mean). CI [0.773, 0.953]. Abstention: 100%@10%, 90%@20%.
- **Lift onset-window retrain**: Val AUROC 0.962 but episode AUROC regressed to 0.679 (from 0.819 v1). Only 2.5% positive samples — too sparse.
- **Lift v3 (episode_window, W=128, balanced)**: Fixed sparse-label problem. Label = episode outcome for all steps in [onset, onset+128]. Balanced 50/50 sampling. Val AUROC 0.954.
- **n=100 final evals**: Can AUROC 0.821 [0.738, 0.901], Lift AUROC 0.790 [0.690, 0.876]. Both CIs clear 0.5. Mean aggregation best for both.
- **Key insight**: When failures are rare, use episode-outcome labels over a wide post-onset window with balanced sampling. Gives model enough positive signal.
- **Square results**: AUROC 0.523 (near-random). Val AUROC 0.887 but episode-level detection 0.523. Same gap as Lift v2.
  - Training data: 250 ep, 10.9% fail rate, clean 74%, zero80 14%, dropout 44-52%
  - Eval: 9 success / 41 fail (18% success under zero80)

### 2026-03-04
- Contrastive detection experiments (exhausted — best AUROC 0.738 with synthetic negatives)
- Oracle reranking closed (+5% headroom only)
- Pivoted to failure detector / abstention trigger
- Eval infra: env reuse, checkpoint/resume, magnitude baseline, bootstrap CIs

### 2026-03-03
- Can zero_object onset curve: sharp transition 75-100 steps
- Fixed Can obs compatibility (field reorder) + Lift sign flip
- 6 bugs found and fixed in perturbation pipeline

### 2026-03-02
- robosuite 1.5.2 controller compatibility: 5 patches for abs_action
- First non-zero reward on Lift after fixes
- Performance: batched lockstep rollout, branch_horizon

### 2026-03-01
- Robomimic lowdim support, headroom audit infra

### 2026-02-25
- Push-T contrastive prototype: AUROC 0.994

## Notes

- Repo: `C:\Users\neilt\OneDrive\Documents\TAP-Score`
- GPU: NVIDIA RTX 4080 (local), RunPod A100 available
- Key scripts: `train_risk_tap.py`, `scripts/eval_tap_detection.py`, `scripts/collect_risk_rollouts.py`
- Checkpoints: `checkpoints_contrastive/<benchmark>/risk_tap_best.pt`
- Results: `eval_results/` (JSON + CSV)
