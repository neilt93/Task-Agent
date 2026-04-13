---
status: active
created: 2026-03-17
---

## Goal

Smart AR assistant for RayNeo Air 4 Pro glasses. Scene understanding, YOLOv8 object detection, MediaPipe hand tracking, MiDaS depth estimation, pyttsx3 voice feedback. Dual modes: assembly copilot and smart assistant.

## Tasks

- [x] YOLOv8 object detection pipeline
- [x] MediaPipe hand tracking
- [x] MiDaS depth estimation
- [x] pyttsx3 voice notifications
- [x] Spatial awareness + proactive suggestions
- [x] 290-test suite
- [ ] Push HUD rendering to device
- [ ] Calibrate assembly copilot for specific kits

## Notes

- GitHub: https://github.com/neilt93/AR-glasses
- Path: `C:\Users\neilt\OneDrive\Documents\AR glasses`
- Stack: Python 3.10+, OpenCV, YOLOv8, MediaPipe, MiDaS, pyttsx3
- Key structure: `src/assistant/`, `src/perception/`, `src/hud/`
- Device: RayNeo Air 4 Pro
