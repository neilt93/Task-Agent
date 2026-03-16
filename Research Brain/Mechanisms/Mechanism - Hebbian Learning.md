---
type: mechanism
id: "mechanism:hebbian_learning"
activation: 0.160
prediction_error: 0.0001
surprise: 0.0000
consolidation_count: 0
tags: [mechanism]
---

# Hebbian Learning

Synapses strengthen when pre and post neurons fire together. 'Neurons that fire together wire together.' Purely local, no global error signal needed.

## Components
- pre-post correlation
- weight update
- no error signal


## Connections

### Incoming
- uses_method: [[Paper - Bio-Inspired Learning with Hebbian Plasticity and Anti-Hebbian Competition]] (w=1.00)
- extends: [[Mechanism - Equilibrium Propagation]] (w=1.00)
- extends: [[Mechanism - STDP (Spike-Timing-Dependent Plasticity)]] (w=1.00)
- uses_method: [[Paper - Dual Propagation Accelerating Contrastive Hebbian Learning with Dyadic Neurons]] (w=1.00)
- uses_method: [[Paper - Intel Loihi 2 A Neuromorphic Processor with Programmable Synaptic Learning]] (w=1.00)
- uses_method: [[Paper - Two Tales of Single-Phase Contrastive Hebbian Learning]] (w=1.00)
### Outgoing
- related_to: [[Mechanism - Anti-Hebbian Competition]] (w=1.00)

## Substrate State
- Activation: 0.160
- Prediction Error: 0.0001
- Surprise: 0.0000
- Consolidation Count: 0