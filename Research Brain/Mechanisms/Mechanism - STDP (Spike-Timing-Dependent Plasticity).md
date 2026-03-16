---
type: mechanism
id: "mechanism:stdp_(spike-timing-dependent_plasticity)"
activation: 0.160
prediction_error: 0.0001
surprise: 0.0000
consolidation_count: 0
tags: [mechanism]
---

# STDP (Spike-Timing-Dependent Plasticity)

Synaptic strength depends on precise timing of pre and post spikes. Pre-before-post strengthens (LTP), post-before-pre weakens (LTD). Temporal Hebbian learning.

## Components
- spike timing
- LTP window
- LTD window
- temporal correlation


## Connections

### Incoming
- uses_method: [[Paper - Backpropagation-Free Spiking Neural Networks with the Forward-Forward Algorithm]] (w=1.00)
- uses_method: [[Paper - Surrogate Gradient Learning in Spiking Neural Networks]] (w=1.00)
- uses_method: [[Paper - Intel Loihi 2 A Neuromorphic Processor with Programmable Synaptic Learning]] (w=1.00)
- extends: [[Mechanism - Eligibility Propagation (E-prop)]] (w=1.00)
### Outgoing
- extends: [[Mechanism - Hebbian Learning]] (w=1.00)

## Substrate State
- Activation: 0.160
- Prediction Error: 0.0001
- Surprise: 0.0000
- Consolidation Count: 0