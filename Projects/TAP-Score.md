---
status: active
created: 2026-03-02
---

## Goal

Off-manifold action detection for visuomotor imitation learning. Train a contrastive scorer (TAP-Score) on expert demonstrations to detect when a Diffusion Policy proposes actions inconsistent with expert behavior. Penn State research project for Prof. Huijuan Xu's Vision-Language Lab.

## Tasks

- [x] SOTA identification (Diffusion Policy) 📅 2026-02-15
- [x] Baseline replication (partial, transparent) 📅 2026-02-20
- [x] Implement contrastive TAP-Score (InfoNCE) 📅 2026-02-22
- [x] Push-T evaluation — AUROC 0.998 📅 2026-02-25
- [x] Push-T reranking with TAP-Score 📅 2026-02-27
- [x] Robomimic (Lift, Can) lowdim support + headroom audit infra 📅 2026-03-01
- [x] Policy stochasticity checks — Lift + Can both diverse 📅 2026-03-02
- [ ] Robomimic headroom audit — Lift (K=4, n=20) 📅 2026-03-02
- [ ] Robomimic headroom audit — Can (K=4, n=20) 📅 2026-03-02
- [ ] Interpret fork decisions → decide if TAP-Score reranking is viable for robomimic
- [ ] If headroom exists: train TAP-Score on robomimic expert data
- [ ] Final report write-up (`report/4_method_and_results.md`)

## Milestones

| Target Date | Milestone | Status |
|---|---|---|
| 2026-02-22 | Contrastive TAP-Score implemented | Done |
| 2026-02-25 | Push-T AUROC > 0.95 | Done (0.998) |
| 2026-03-01 | Robomimic infra ready | Done |
| 2026-03-02 | Headroom audits running | In progress |
| TBD | Fork decision for robomimic | Pending |
| TBD | Final report | Pending |

## Architecture

**Two-Encoder Contrastive Model (InfoNCE)**
- Observation encoder: CNN (images) or MLP (state)
- Action encoder: MLP on action chunks
- Scoring: dot product of normalized embeddings
- Loss: InfoNCE — rank correct action above M negatives
- Inference: log-margin score = `log_prob(correct) - logsumexp(negatives)`

## Results

### Push-T (image, 96x96, action_dim=2)
- **AUROC: 0.998** — near-perfect separation of success vs held-out failures
- Operating points: TPR 98.5% @ 1% FPR, 99.2% @ 5% FPR
- Stable across M ablation (7, 15, 31 negatives)
- Prefix-only AUROC (first 70%): 0.994 — strong early warning

### Robomimic Headroom Audit (in progress)

**Stochasticity checks** (2026-03-02):
- Lift: mean L2 spread 0.0042 — diverse, valid for best-of-K
- Can: mean L2 spread 0.0050 — diverse, valid for best-of-K

**Headroom audits** (running):
- Config: K=4, decision_interval=10, skip_first=2, n_episodes=20
- Batched lockstep rollout optimization (~133s/episode)
- Lift: running (~90 min ETA)
- Can: queued after Lift (~90 min ETA)

## Research Directions

1. **Reranking at inference time** — If headroom exists, use TAP-Score to select best-of-K action candidates instead of defaulting to K=1. Already proven on Push-T.
2. **Cross-benchmark generalization** — Does contrastive scoring transfer across tasks? Shared encoder architecture suggests possible zero-shot transfer.
3. **Phase-conditioned scoring** — Weight scores differently based on task phase (approach vs grasp vs place). Could improve reranking on manipulation tasks.
4. **Online adaptation** — Fine-tune scorer on deployment data to handle distribution shift.
5. **Intervention triggers** — Use TAP-Score as a safety monitor: if score drops below threshold, switch to recovery policy or request human help.

## Log

### 2026-03-02
- PC crashed during initial headroom audit attempt (K=8, di=5, parallel lift+can)
- Diagnosed bottleneck: K=8 threaded branching with ThreadPoolExecutor was serializing on GPU (GIL contention)
- Optimized `evaluate_candidates` to use **batched lockstep rollout** — single `predict_action(batch_size=K)` per continuation step instead of K individual calls
- Added: `torch.inference_mode()`, OMP/MKL thread capping, per-episode timing logs
- Reduced params: K=4, di=10, n=20 — ~10x faster than original config
- Episode timing: ~133s/ep on RTX 4080. Full pipeline (Lift+Can) ETA ~3 hours
- Both stochasticity checks passed — policies produce diverse candidates
- **CRITICAL BUG FOUND**: All 80 episodes (Lift+Can) returned 0% success, 0 reward
  - Root cause: `control_delta=False` was never set in env_meta for abs_action checkpoints
  - The pretrained DP checkpoints output absolute poses, but robosuite controller defaulted to delta mode
  - Robot flew out of workspace on every step — absolute coordinates interpreted as relative offsets
  - One-line fix: `env_meta['env_kwargs']['controller_configs']['control_delta'] = False`
  - All prior headroom results are invalid — must re-run

### 2026-03-01
- Added robomimic (Lift, Can) lowdim support
- Created headroom audit infrastructure
- Downloaded pretrained DP checkpoints (~1GB each)
- Created Windows setup scripts (`scripts/windows/`)

### 2026-02-27
- Push-T reranking evaluation complete
- Label spread gate analysis

### 2026-02-25
- Push-T contrastive TAP-Score evaluation
- AUROC 0.998, strong operating points

## Notes

- Repo: `C:\Users\neilt\OneDrive\Documents\TAP-Score`
- GPU: NVIDIA RTX 4080 (local), RunPod A100 available if needed
- Key scripts: `train_contrastive_tap.py`, `eval_tap_final.py`, `scripts/robomimic_headroom_audit.py`
- Checkpoints: `checkpoints_contrastive/<benchmark>/contrastive_tap_best.pt`
- Results: `eval_results/` (JSON)
