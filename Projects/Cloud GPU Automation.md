---
status: active
---
	
## Goal
Chat-first task runner for AI engineering — describe tasks in natural language, get agent + GPU recommendations, approve, watch execution with progressive disclosure.

## Tasks
- [x] MVP scaffold end-to-end (backend + frontend wiring)
- [x] Real pipeline executor with phase-based execution
- [x] RunPod adapter + Claude Code agent adapter
- [x] Async store (memory + SQLite), secret redaction, cost tracking
- [x] Reliability hardening Phase 1: persist instance metadata, shield cleanup, reconciler terminates orphan pods, redaction tests (25 tests)
- [x] Reliability hardening Phase 2: cancelled/timed_out statuses, cancel button, status badges, failure scenario tests (14 tests)
- [ ] Run and record real happy-path demo (RunPod + Claude Code)
- [ ] Stress-test failure paths (SSH drop, agent crash, cleanup verification)
- [ ] Create 5-task benchmark sheet (completion rate, time, retries, cost)
- [ ] Verify zero frontend breakage with all event types

## Notes
- 39 backend tests passing (redaction, executor cancel/TTL/cleanup, pipeline rollback/cost-cap)
- Frontend builds cleanly with zero TS errors
- GPU instances now survive cancellation/crashes thanks to shielded cleanup + emergency terminate fallback
- Reconciler terminates orphan pods on startup using persisted instance_id/provider
- Cancel and timeout get distinct statuses (orange/amber badges) instead of generic "failed"
