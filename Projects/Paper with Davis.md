---
status: submitted
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
- [x] Address remaining reviewer feedback (submission readiness pass)
- [x] Make repo submission-ready (LICENSE, requirements.txt, .env.example, .gitignore, README)
- [x] Fix prompt block rendering (switch to lstlisting, no wrap markers)
- [x] Fix licensing wording (code=MIT, data=CC BY 4.0)
- [x] Fix paper date, author block (NYU affiliation + nt2439@nyu.edu)
- [x] Get arXiv endorsement for cs.AI
- [x] Submit to arXiv 📅 2026-03-03
- [x] Blog post: VLM visibility benchmark preview 📅 2026-03-03
- [x] Tweet thread (3 tweets + chart) 📅 2026-03-03
- [ ] Add arXiv link to tweet thread once live (~24h)

## Notes
- **Repo:** https://github.com/neilt93/Paper-with-Davis
- **Latest commit:** `443e544` — Switch prompt block from Verbatim to lstlisting
- **arXiv:** Submitted 2026-03-03 (cs.AI, CC BY 4.0)
- **9 models (3/3/3):**
  - Flagship: Gemini 3.1 Pro, GPT-5, Claude Opus 4.5
  - Prior-gen: GPT-4o, Gemini 2.5 Pro, Claude 3.7 Sonnet
  - Open-source: Gemma 3 12B, InternVL3-8B, Qwen3-VL-8B
- **Top scores:** GPT-4o (0.728) and Gemini 3.1 Pro (0.727) effectively tied
- **Key finding:** GPT-5 has 78 abstentions (26% of headline items), dragging composite to 0.625 despite highest answered accuracy (0.851)
- **Dataset:** `Sheets/results.csv` (100 families, all Status=Done)
