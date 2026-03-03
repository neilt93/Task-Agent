---
status: active
---

## Goal
Publish VB (Visibility Benchmark) paper evaluating 9 VLMs on visibility reasoning with 2x2 XOR design, 100 families, 300 headline cells.

## Tasks
- [x] Re-score all 9 models on frozen 100-family dataset
- [x] Run fresh Gemini 2.5 Pro inference (replaced mystery gemini file)
- [x] Fix Table 1 category counts (OF: 16->15, NV: 10->11)
- [x] Fix ToMAcc section (7 families / 21 items, was 8/24)
- [x] Update paper from 10 to 9 models with 3/3/3 tier split
- [x] Update all tables, scores, and prose throughout paper
- [x] Push updated paper to GitHub
- [ ] Address remaining reviewer feedback (submission readiness pass)
- [ ] Submit to arXiv / target venue

## Notes
- **Repo:** https://github.com/neilt93/Paper-with-Davis
- **Latest commit:** `5ef0be4` — Re-score all models and update paper to 9-model lineup
- **9 models (3/3/3):**
  - Flagship: Gemini 3.1 Pro, GPT-5, Claude Opus 4.5
  - Prior-gen: GPT-4o, Gemini 2.5 Pro, Claude 3.7 Sonnet
  - Open-source: Gemma 3 12B, InternVL3-8B, Qwen3-VL-8B
- **Top scores:** GPT-4o (0.728) and Gemini 3.1 Pro (0.727) effectively tied
- **Key finding:** GPT-5 has 78 abstentions (26% of headline items), dragging composite to 0.625 despite highest answered accuracy (0.851)
- **Dataset:** `Sheets/results.csv` (100 families, all Status=Done)
