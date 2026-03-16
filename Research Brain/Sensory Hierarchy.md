---
status: active
created: 2026-03-09
tags: [neuroscience, connectome, drosophila, architecture, sensory]
project: "[[Connectome Fly Brain]]"
---

## The Sensory Hierarchy

A real fly brain expects multiple sensory layers. We currently have only Layer 1.

### Layer 1: Proprioception (DONE)
**Status: Implemented and validated**

| Channel | Neurons | Input | What It Does |
|---|---|---|---|
| Gustatory | 20 sugar GRNs | Contact forces (global mean) | Feeding context |
| Proprioceptive | 33 SEZ ascending | Joint angles + velocities | Body state awareness |
| Mechanosensory | 15 SEZ ascending | Per-leg contact forces | Ground contact sensing |
| Vestibular | 7 SEZ ascending | Body velocity + orientation | Balance/inertia |

**Result**: Fly walks, responds to perturbations, turn circuits activate under asymmetric input (9/10 tests pass).

### Layer 2: Exteroception (NEXT)
**Status: Available in NeuroMechFly v2**

#### Vision (~9,800 neurons)
- ~700 columns per compound eye
- Each column has ~14 neuron types (L1, L2, Mi1, Tm3, etc.)
- Input: light intensity and motion per ommatidium
- NeuroMechFly v2 has **FlyVision** already built in
- Maps to visual neurons in FlyWire connectome
- **Enables**: phototaxis, obstacle avoidance, looming detection, optic flow navigation

#### Olfaction (~50 receptor types)
- Odor concentration at each antenna
- Maps to antennal lobe neurons in FlyWire
- **Enables**: chemotaxis, food seeking, mate finding, predator avoidance

### Layer 3: Specialized Senses (FUTURE)

#### Detailed Mechanosensory
- Wind/air pressure on body hairs
- Johnston's organ on antennae (detects movement, gravity, wind)
- More detailed than per-leg contact forces
- **Enables**: wind-guided navigation, flight control

#### Nociception
- Heat and pain neurons
- **Enables**: escape behavior, avoidance learning

## What Each Layer Unlocks

| Layer | Behavior |
|---|---|
| Proprioception alone | Walking, gait modulation, perturbation response |
| + Vision | Navigation, obstacle avoidance, phototaxis |
| + Olfaction | Chemotaxis, food seeking |
| + Mechanosensory | Wind response, fine-grained environmental sensing |
| All together | Rich naturalistic behavior |

## Integration Plan

### Step 1: Map NeuroMechFly v2 sensory outputs to FlyWire neuron IDs
- FlyVision outputs -> visual neuron populations in connectome
- Olfactory outputs -> antennal lobe neuron populations
- Need Ramdya lab's mapping or build our own from FlyWire annotations

### Step 2: Extend SensoryEncoder
- Add `visual` and `olfactory` channels to `channel_map.json`
- Map FlyVision outputs to Poisson rates for visual neurons
- Map odor concentration to rates for olfactory neurons

### Step 3: Expand readout population
- Vision-driven behavior needs different descending neurons
- FlyWire DN annotations (1,316 neurons) include visual-responsive DNs
- Expand from 47 to ~200 readout neurons

### Step 4: New experiments
- Phototaxis: light source -> does the fly navigate toward it?
- Chemotaxis: odor gradient -> does the fly follow it?
- Obstacle avoidance: visual looming -> does the fly turn?

## Ramdya Lab Pitch

**We built the brain side (motor control layer). They built the sensory side (vision + olfaction in NeuroMechFly v2). Together we close the full sensorimotor loop.**

Specific collaboration value:
1. Their FlyVision module -> our brain model's visual neuron populations
2. Their olfactory module -> our brain model's antennal lobe neurons
3. Our descending decoder + CPG bridge -> their body model
4. Joint paper: first full sensory-brain-motor closed loop through real connectome

## Related

- [[Connectome Fly Brain]] — parent project
- [[Sensory Perturbation Experiment]] — proved Layer 1 works, motivates Layer 2
- [[Eon Systems - Fly Brain Emulation]] — they have the brain but limited sensory input
