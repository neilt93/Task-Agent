---
type: mechanism
id: "mechanism:eligibility_propagation_(e-prop)"
activation: 1.000
prediction_error: 0.0001
surprise: 0.0000
consolidation_count: 0
tags: [mechanism]
---

# Eligibility Propagation (E-prop)

Online learning for recurrent SNNs using eligibility traces. Avoids BPTT by tracking local eligibility of synapses, modulated by a global learning signal.

## Components
- eligibility traces
- local synapse tracking
- global modulation
- online updates


## Connections

### Incoming
- uses_method: [[Paper - E-prop Eligibility Propagation for Recurrent Spiking Networks]] (w=1.00)
### Outgoing
- extends: [[Mechanism - STDP (Spike-Timing-Dependent Plasticity)]] (w=1.00)

## Substrate State
- Activation: 1.000
- Prediction Error: 0.0001
- Surprise: 0.0000
- Consolidation Count: 0