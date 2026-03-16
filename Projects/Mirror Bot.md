---
status: on-hold
---

## Goal

Real-time animated talking-head mirror — see your own face animated in real-time, speaking LLM responses in your cloned voice. Local-first on RTX 4080.

## Architecture

```
CLI (TypeScript)  →  Ollama LLM  →  XTTS v2 TTS  →  Face Animation  →  PyGame Display
                     (dolphin-mistral /                (SadTalker or
                      llama3.1:8b)                      GaussianTalker)
```

Backend switchable via `LIPSYNC_BACKEND` env var (`sadtalker` or `gaussian`).

## Current Status

**SadTalker pipeline works end-to-end** but is the bottleneck: 10.8 fps render (needs 25 fps for realtime). TTS and LLM are fast.

**GaussianTalker integration code complete** (March 2026) — all files written, CUDA kernels compiled, engine wired in. Waiting on capture video to preprocess, train, and benchmark.

## Tasks

### Avatar v1: SadTalker (complete)
- [x] SadTalker lip sync engine with XTTS v2 voice clone
- [x] FastAPI server (port 9876) + PyGame display window
- [x] Sentence-chunked TTS+render for streaming feel
- [x] Compatibility fixes (PyTorch 2.7, numpy, torchvision, transformers, ffmpeg on Windows)
- [x] Benchmark harness (`avatar/benchmark.py`)

### Avatar v2: GaussianTalker (code complete, needs data)
- [x] Plan and design 7-phase integration
- [x] Create `avatar/gaussian/` package (setup, preprocess, train, engine)
- [x] Install CUDA Toolkit 12.8, compile diff-gaussian-rasterization + simple-knn
- [x] Clone GaussianTalker repo with submodules
- [x] Write `GaussianTalkerEngine` matching actual vendor API (render_from_batch, Camera objects, ModelHiddenParams)
- [x] Wire backend switch into config.py, server.py, lipsync/__init__.py, benchmark.py
- [ ] Record ~15 min capture video (1080p, 60fps, tripod, even lighting)
- [ ] Preprocess: `python avatar/gaussian/preprocess.py --input data/avatar/capture/neil_capture.mp4`
- [ ] Smoke train: `python avatar/gaussian/train.py --max-frames 1500 --iters 2000`
- [ ] Full train: `python avatar/gaussian/train.py` (~4-6 hours on RTX 4080)
- [ ] Test: `set LIPSYNC_BACKEND=gaussian` and run server
- [ ] Benchmark: compare against SadTalker's 10.8 fps baseline (target: 50-80 fps)

### Earlier Phases (complete)
- [x] GPU service structure (STT, TTS, animator FastAPI routers)
- [x] Node.js API server (WebSocket, REST, streaming pipeline)
- [x] Browser UI (React + Vite, video queue, onboarding, memory panel)
- [x] Cloud deployment provisioner (Vast.ai)

## Key Files

| File | Purpose |
|---|---|
| `avatar/server.py` | FastAPI server, backend-conditional engine import |
| `avatar/config.py` | `LIPSYNC_BACKEND`, `GAUSSIAN_MODEL_DIR` config |
| `avatar/lipsync/sadtalker_engine.py` | SadTalker engine (current default) |
| `avatar/gaussian/engine.py` | GaussianTalker engine (drop-in replacement) |
| `avatar/gaussian/preprocess.py` | Video → dataset pipeline (9 stages) |
| `avatar/gaussian/train.py` | Training launcher (RTX 4080 defaults) |
| `avatar/gaussian/setup_gaussian.py` | Environment setup automation |
| `avatar/benchmark.py` | Backend-aware benchmark harness |

## Notes

- All code lives in `Digital-persona/` — existing CLI, orchestrator, memory, and models are untouched
- ML models: XTTS v2 (~2GB VRAM), SadTalker (~4GB), GaussianTalker (TBD, gradient checkpointing enabled for 16GB)
- CUDA Toolkit 12.8 installed at `C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v12.8`
- GaussianTalker vendor code at `avatar/vendor/GaussianTalker/`
- Run with: `npx tsx bin/mirror.ts --model ollama:dolphin-mistral --avatar`
- Project is on hold per March 2026 priorities (job search, TAP-Score, Davis paper come first)
