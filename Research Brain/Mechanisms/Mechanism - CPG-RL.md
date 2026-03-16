---
type: mechanism
id: "mechanism:cpg-rl"
activation: 1.000
prediction_error: 0.0003
surprise: 0.0000
consolidation_count: 0
tags: [mechanism]
---

# CPG-RL

Central Pattern Generator oscillators integrated with deep RL. CPG provides structured motor primitives, RL modulates parameters. Combines structural priors with learned adaptation.

## Components
- coupled oscillators
- RL parameter modulation
- phase coordination
- gait selection


## Connections

### Incoming
- uses_method: [[Paper - CPG-RL Learning Central Pattern Generators for Quadruped Locomotion]] (w=1.00)
- uses_method: [[Paper - AllGaits Learning All Quadruped Gaits and Transitions]] (w=1.00)
### Outgoing
- extends: [[Mechanism - Central Pattern Generator]] (w=1.00)

## Substrate State
- Activation: 1.000
- Prediction Error: 0.0003
- Surprise: 0.0000
- Consolidation Count: 0