---
status: active
---

## Goal

Contrastive scorer for off-manifold action detection in visuomotor imitation learning. Two stories: passive detection (AUROC 0.998 on Push-T) and active best-of-K reranking. Extending to robomimic (Lift, Can) lowdim tasks.

## Tasks

### Robomimic extension (current)
- [ ] Run `setup_runpod.sh` on RunPod (downloads data + pretrained DP checkpoints)
- [ ] Run `inspect_robomimic_lowdim.py --task all` preflight
- [ ] Run `check_policy_stochasticity.py` on lift checkpoint (critical gate)
- [ ] Run headroom audit on Lift (`run_robomimic_headroom_audit.sh`)
- [ ] Run headroom audit on Can
- [ ] Make fork decision: if meaningful headroom, train TAP on robomimic demos

### Push-T reranking (scaling)
- [ ] Scale reranking eval to n=200 episodes (prob_occlusion, K=4)
- [ ] Compute delta success rate with CI, rescues, harms, net benefit
- [ ] If positive: try K=8 or margin gate to amplify

### Paper
- [ ] Write up passive detection results
- [ ] Write up reranking results (if effect holds)
- [ ] If robomimic headroom exists, add cross-task generalization section

## Notes

- Pretrained Diffusion Policy (not BC-RNN) checkpoints found for lift/can lowdim — stochastic by nature, no training needed for audit
- Checkpoint paths: `data/robomimic/checkpoints/lift_ph_diffusion_policy_cnn.ckpt`, `can_ph_diffusion_policy_cnn.ckpt`
- Benchmarks registered as `robomimic_lift_lowdim` and `robomimic_can_lowdim` (namespaced for future image variants)
- HDF5 lowdim data loader + contrastive dataset class ready in `tap/contrastive.py` and `tap/data_loaders.py`
- Fixed lexicographic demo sort bug across all HDF5 loaders (demo_10 before demo_2)
- Push-T passive detection: AUROC ~0.998, n=50 reranking: K=4 TAP 30% vs notap 26% (3 true rescues)
- TAP H=8 checkpoint: `checkpoints_contrastive/pusht/contrastive_tap_h8_best.pt`
