---
status: active
---

## Goal

Real-time animated talking-head mirror — see your own face animated in real-time, speaking LLM responses in your cloned voice. Browser UI on Mac, heavy ML on a GPU (Vast.ai or local NVIDIA).

## Architecture

```
Browser (Mac)  ←WebSocket→  Node.js API Server  ←HTTP→  Python GPU Service (NVIDIA)
                             (orchestrator,               (STT, TTS, face animation)
                              memory, chat)
```

## Tasks

### Phase 1: GPU Service (Python)
- [x] Create gpu-service/ directory structure, Dockerfile, requirements.txt
- [x] Implement STT model wrapper (Faster-Whisper large-v3)
- [x] Implement TTS model wrapper (Coqui XTTS v2 with voice enrollment)
- [x] Implement animator wrapper (SadTalker with 3DMM caching + ffmpeg fallback)
- [x] Implement FastAPI routers (stt, tts, animate, onboard, health)
- [x] Create download_models.py for baking weights into Docker image
- [ ] Build and push Docker image to registry
- [ ] Test standalone: curl /tts → valid WAV, curl /animate → valid MP4

### Phase 2: Node.js API Server
- [x] Create gpu-client.ts (HTTP client for GPU service)
- [x] Create streaming.ts (sentence splitter + TTS→animate pipeline)
- [x] Create Fastify server with WebSocket + REST routes
- [x] Create bin/server.ts entry point (init orchestrator, start Fastify)
- [x] Add routes: chat (WS), memories, photos, health
- [x] Add Fastify deps to package.json, new types to src/types.ts
- [ ] Test: WebSocket text in → video chunks out

### Phase 3: Browser UI (React + Vite)
- [x] Scaffold web/ with Vite + React 18 + TypeScript
- [x] Build AvatarCanvas (video queue) + useVideoQueue hook
- [x] Build useWebSocket hook with auto-reconnect
- [x] Build ChatInput + MicButton + useAudioCapture (VAD)
- [x] Build SubtitleOverlay + CitationPanel
- [x] Build OnboardingWizard (photo upload + voice recording)
- [x] Build MemoryPanel + SettingsPanel
- [ ] Run `npm install` and `npm run build` in web/
- [ ] End-to-end test in browser

### Phase 4: Cloud Deployment
- [x] Create VastAiGpuService provisioner (vastai-gpu-service.ts)
- [ ] Build + push Docker image for GPU service
- [ ] Test GPU_AUTO_PROVISION mode on Vast.ai
- [ ] Test: `npm run serve` on Mac, GPU auto-provisions on Vast.ai

## Notes

- All code lives in `Digital-persona/` — existing CLI, orchestrator, memory, and models are untouched
- ML models: Faster-Whisper (~3GB VRAM), XTTS v2 (~2GB), SadTalker (~4GB) — fits on 16GB+ GPU
- TypeScript compiles clean, 63/64 existing tests pass (1 pre-existing timing flake)
- Estimated time to first avatar frame on RTX 4090: ~4.3s (STT 500ms + LLM 1.5s + TTS 300ms + SadTalker 2s)
- Start server with `npm run serve`, dev UI with `cd web && npm run dev`
