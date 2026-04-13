---
status: active
created: 2026-03-14
---

## Goal

Benchmark testing whether LLMs recognise when their context is stale. The headline finding: more capable models are *more* confidently wrong when timestamps are absent. Scaling makes temporal blindness worse.

## Tasks

- [x] Fine-tune GPT-2 on text_time task: 54.6% accuracy, 81.2% switch sensitivity 📅 2026-03-14
- [x] Fine-tune TinyLlama: 70.7% accuracy, 70.8% switch sensitivity 📅 2026-03-14
- [x] Frontier eval: GPT-5.4 shows 100% false trust without timestamps, 0% with 📅 2026-03-14
- [x] Frontier eval: Claude Opus 84.6% false trust without, 0% with, 100% switch sensitivity 📅 2026-03-14
- [ ] Mistral local eval (in progress)
- [ ] Phase 0 verification + v3 data scaling (in progress)
- [ ] Scale to 500+ test cases
- [ ] Write paper: intro, method, results (fine-tuned + frontier), discussion
- [ ] Generate figures: monotonic policy curves, scaling comparison, frontier heatmap
- [ ] Circulate draft for feedback
- [ ] Submit to arXiv

## Milestones

| Target Date | Milestone | Status |
|---|---|---|
| 2026-03-14 | GPT-2/TinyLlama fine-tune results | Done |
| 2026-03-14 | Frontier eval on GPT-5.4 + Claude Opus | Done |
| 2026-03-27 | arXiv submission | Pending |

## Key Finding

The mic drop result: scaling makes temporal blindness *worse*. Frontier models trust stale context more confidently than smaller models, unless timestamps are explicitly provided. This reverses the usual "bigger is better" narrative for a specific failure mode.

## Notes

- GitHub: https://github.com/neilt93/temporal-bench
- Top priority project per One Month Plan - March 2026
- Already featured on CV (Research & Publications section)
- Stack: Python, PyTorch, HuggingFace transformers, frontier API clients
- The paper is the Week 3 focus of the March plan
