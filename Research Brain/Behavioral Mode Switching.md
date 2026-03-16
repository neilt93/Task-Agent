---
status: planned
created: 2026-03-09
tags: [neuroscience, connectome, drosophila, experiment-design, publishable]
project: "[[Connectome Fly Brain]]"
---

## Question

Does the same connectome switch behavioral modes (walking vs flight) based on sensory context?

## Hypothesis

The fly brain has both walking and flight motor circuits. Currently we drive walking proprioception (joint angles, contact forces) so the brain activates walking circuits. If we instead drove flight-associated sensory input (haltere signals, visual optic flow), the flight motor circuits should activate — demonstrating emergent behavioral mode selection from sensory context alone, with no explicit mode-switching logic.

## Why This Is Publishable

This would demonstrate that **behavioral mode selection emerges from connectome wiring**. The same 139k-neuron network switches between walking and flight based purely on which sensory neurons are active. No engineered mode selector, no behavioral state machine — just the wiring.

## Experimental Design

### Walking mode (current baseline)
- **Sensory input**: proprioceptive (33 neurons) + mechanosensory (15) + vestibular (7) + gustatory (20) = 75 neurons
- **Expected output**: walking motor neurons active, forward/turn descending commands

### Flight mode (new)
- **Sensory input**:
  - Haltere signals (Johnston's organ neurons — JON-CE, JON-F, JON-Dm, ~146 neurons already identified in figures.ipynb)
  - Visual optic flow (R7/R8 photoreceptors + T4/T5 motion detectors)
  - Wind/airflow on antennae (mechanosensory — Johnston's organ)
- **Expected output**: flight motor neurons active (different descending neuron population)

### Flight readout neurons
Need to identify flight-specific descending neurons in FlyWire:
- DNp09, DNp11, DNg13 — known flight-initiating descending neurons
- Wing motor neurons in thorax
- Haltere motor neurons
- Use `flywire_annotations_matched.csv` to find these by cell_type

### Protocol
1. Run baseline with walking sensory input → record which readout neurons are active
2. Switch to flight sensory input → record which readout neurons are active
3. Compare: do different descending neuron populations activate?
4. Test transition: start with walking input, switch mid-run to flight input

### Key Controls
- Same brain model (139k neurons, same connectome)
- Same simulation parameters
- Only the sensory input changes
- Measure which descending neuron groups (from the full 1,316 DN annotations) activate in each mode

## Implementation Notes

### FlyWire neuron populations needed
From `brain-model/flywire_annotations_matched.csv`:
- Johnston's organ neurons: search for "JON" in cell_type (~146 neurons from figures.ipynb)
- Flight descending neurons: search for "DNp09", "DNp11", "DNg13" in cell_type
- Wing motor neurons: search for "wing" or "flight" in cell_type
- T4/T5 motion detectors: already found in optic lobe annotations (~6,000+ neurons)

### Sensory encoding for flight
- Haltere input: simulate oscillating signal at wing beat frequency (~200 Hz) with perturbation
- Visual optic flow: simulate expanding/contracting optic flow pattern
- No contact forces (fly is in the air)

## Connection to Other Results

- **Chemotaxis experiment**: Proved the connectome responds to olfactory input (avoidance behavior)
- **Sensory perturbation**: Proved asymmetric input activates turn circuits
- **This experiment**: Would prove the connectome has multiple behavioral modes accessible through different sensory channels

## Paper Narrative

> "The same connectome switches between walking and flight behavioral programs based on sensory context alone. Walking proprioception drives locomotor descending neurons, while haltere and visual optic flow signals activate flight descending neurons — demonstrating emergent behavioral mode selection from connectome wiring without any explicit mode-switching logic."

## Related

- [[Connectome Fly Brain]] — parent project
- [[Sensory Hierarchy]] — sensory modalities available
- [[Sensory Perturbation Experiment]] — proved asymmetric input drives behavior
