---
status: active
created: 2026-03-09
tags: [neuroscience, connectome, whole-brain-emulation, drosophila, mujoco]
---

## What They Did

Eon Systems (San Francisco) demonstrated the **first embodied whole-brain emulation** — a literal neuron-by-neuron, synapse-by-synapse copy of the fruit fly brain driving a physics-simulated body through multiple naturalistic behaviors.

**"Structure encodes behaviour"** — the connectome wiring alone, without learning or tuning, predicts 95% of motor behavior.

## Technical Stack

| Component | Detail |
|---|---|
| Connectome | FlyWire — 125,000 neurons, 50M synaptic connections |
| Neuron model | Leaky integrate-and-fire (LIF) spiking network |
| Neurotransmitter ID | ML predictions from EM data |
| Simulators | Brian2, Brian2CUDA, PyTorch, NEST GPU, neuromorphic chips |
| Physics / body | **MuJoCo** via `flybody` (forked from DeepMind) |
| Accuracy | 95% motor behavior prediction |

## How It Works

1. Full connectome from FlyWire electron microscopy data
2. Each neuron → LIF model, each synapse → weighted connection from connectome
3. Sensory input flows in from simulated body sensors
4. Neural activity propagates through the complete 125k-neuron network
5. Motor commands flow out to MuJoCo fly body
6. Body executes movement, generating new sensory input → closed loop

## Key Distinction

Previous approaches either:
- Modeled disembodied brains (no body feedback)
- Used RL to train controllers (learned, not copied)

Eon's approach: **no learning at all**. The connectome structure IS the controller. Wiring predicts behavior.

## Team / Advisors

- **Philip Shiu** — senior scientist, lead on 2024 Nature paper
- **Advisors**: George Church, Konrad Kording, Stephen Wolfram, Anders Sandberg, Robin Hanson

## Roadmap

Fly (125k neurons) → Mouse (70M neurons) → Human
Currently amassing mouse connectomic data via expansion microscopy + calcium/voltage imaging.

## GitHub Repos

- [fly-brain](https://github.com/eonsystemspbc/fly-brain) — Brain emulation (Brian2, PyTorch, NEST)
- [drosophila_brain_model_lif](https://github.com/eonsystemspbc/drosophila_brain_model_lif) — LIF connectome model (MIT license)
- [flybody](https://github.com/eonsystemspbc/flybody) — MuJoCo fly body (forked from DeepMind, Apache-2.0)

## Relevance to Connectome Fly Brain Project

Our project uses the same physics engine (MuJoCo via flygym) and the same anatomical model (NeuroMechFly).

### What we share
- Same connectome data (FlyWire, 139k neurons)
- Same neuron model (Brian2 LIF)
- Same physics (MuJoCo via flygym/flybody)
- Same closed-loop architecture (sensory -> brain -> motor -> body)

### What we add beyond Eon
1. **Sensory perturbation analysis** — Eon showed connectome predicts behavior. We ask: *what sensory conditions activate specific motor programs?* (See [[Sensory Perturbation Experiment]])
2. **Hebbian plasticity** — Can adaptive weights improve on fixed connectome for novel terrains?
3. **Gait initialization finding** — Brain signals alone insufficient for locomotion. VNC-level phase patterns must be initialized independently. This is a real neuroscience result about brain-VNC interface.
4. **Causal ablation** — 10/10 tests pass. Removing specific descending neuron groups causes predictable behavioral changes.

### Our current stack
- 139k-neuron Brian2 LIF brain (same as Eon)
- 75 sensory neurons (4 channels: gustatory, proprioceptive, mechanosensory, vestibular)
- 47 readout descending neurons (5 decoder groups)
- CPG locomotion bridge (to be replaced with VNC connectome model)
- Walking speed: ~21.9 mm/s (real Drosophila: ~20-30 mm/s)

## Sources

- [Eon Systems](https://eon.systems/)
- [The First Multi-Behavior Brain Upload (Substack)](https://theinnermostloop.substack.com/p/the-first-multi-behavior-brain-upload)
- [Shiu et al. 2024 — Whole-brain annotation (Nature)](https://www.nature.com/articles/s41586-024-07686-5)
- [FlyWire Connectome (Nature)](https://www.nature.com/immersive/d42859-024-00053-4/index.html)
- [Berkeley News — Simulate entire fly brain](https://news.berkeley.edu/2024/10/02/researchers-simulate-an-entire-fly-brain-on-a-laptop-is-a-human-brain-next/)
