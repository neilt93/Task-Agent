---
status: on-hold
created: 2025-12-30
---

## Goal

24M-parameter decoder-only transformer trained from scratch on personal chat data (iMessage + Instagram). Full pipeline: extraction, PII filtering, custom BPE tokenisation, training, generation.

## Tasks

- [x] iMessage + Instagram extraction
- [x] PII filter pass
- [x] Data preprocessing
- [ ] Custom BPE tokenisation
- [ ] Training loop
- [ ] Generation sampling
- [ ] QLoRA fine-tune of 3B-7B base model for better quality (deferred)

## Notes

- GitHub: https://github.com/neilt93/NeilGPT
- Path: `C:\Users\neilt\OneDrive\Documents\NeilGPT`
- Stack: Python, PyTorch (from-scratch), BPE, FastAPI, HuggingFace Spaces
- Stale: last substantive commit Dec 2025. Preprocessing done, training pending.
- Per March 2026 plan: parked until after core papers ship; then upgrade path is QLoRA on a 3B-7B base model.
