---
status: active
created: 2026-03-14
---

## Goal

Waveshare HexArth 18-DOF hexapod robot used as the sim-to-real target for the Connectome Fly Brain controller. Closed-loop proprioception, serial protocol bridge for Dynamixel servos, connectome-derived locomotion in hardware.

## Tasks

- [x] Order HexArth (~$500) 📅 2026-03-14
- [x] HexArth serial protocol for Dynamixel servos (2026-04-08 sprint)
- [ ] Buy 3S2P 18650 battery pack (~$30) 📅 2026-03-19
- [ ] Unboxing + first servo test 📅 2026-03-24
- [ ] Calibrate 18 DOF
- [ ] Connectome controller running on hardware
- [ ] Demo video

## Notes

- Path: `C:\Users\neilt\OneDrive\Documents\Hexarth Agent` (hexarth-sim)
- Linked project: [[Connectome Fly Brain]]
- Sim structure: `envs/`, `policies/`, `training/`, `evaluation/`, `dashboard/`, `tests/`
- The pitch for the Ramdya call: connectome-derived controller transferring from 13k-neuron MANC simulation to real hexapod.
