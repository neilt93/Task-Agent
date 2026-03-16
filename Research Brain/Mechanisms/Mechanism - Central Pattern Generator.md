---
type: mechanism
id: "mechanism:central_pattern_generator"
activation: 0.160
prediction_error: 0.0001
surprise: 0.0000
consolidation_count: 0
tags: [mechanism]
---

# Central Pattern Generator

Network of oscillators that produces rhythmic motor patterns through topology alone. No external input needed for basic patterns. Structure IS the program.

## Components
- oscillator neurons
- reciprocal inhibition
- network topology
- phase coupling


## Connections

### Incoming
- uses_method: [[Paper - CPG-RL Learning Central Pattern Generators for Quadruped Locomotion]] (w=1.00)
- uses_method: [[Paper - Whole-Brain Connectome-Based Simulation of Drosophila Motor Behavior]] (w=1.00)
- related_to: [[Mechanism - Diffusion Policy]] (w=1.00)
- extends: [[Mechanism - CPG-RL]] (w=1.00)
- uses_method: [[Paper - AllGaits Learning All Quadruped Gaits and Transitions]] (w=1.00)
- uses_method: [[Paper - Central Pattern Generators for Locomotion Control in Animals and Robots]] (w=1.00)
### Outgoing
- related_to: [[Mechanism - Structural Plasticity]] (w=1.00)

## Substrate State
- Activation: 0.160
- Prediction Error: 0.0001
- Surprise: 0.0000
- Consolidation Count: 0