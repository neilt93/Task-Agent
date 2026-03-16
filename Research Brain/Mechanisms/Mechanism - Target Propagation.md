---
type: mechanism
id: "mechanism:target_propagation"
activation: 0.160
prediction_error: 0.0000
surprise: 0.0000
consolidation_count: 0
tags: [mechanism]
---

# Target Propagation

Each layer receives a target activation (not a gradient). Targets propagated through approximate inverses. More biologically plausible than backprop.

## Components
- layer targets
- approximate inverses
- local loss functions


## Connections

### Incoming
- uses_method: [[Paper - Scaling Supervised Local Learning with Augmented Auxiliary Networks (AugLocal)]] (w=1.00)
- related_to: [[Mechanism - Dendritic Localized Learning]] (w=1.00)
- related_to: [[Mechanism - Feedback Alignment]] (w=1.00)
- uses_method: [[Paper - Target Propagation for Deep Learning]] (w=1.00)

## Substrate State
- Activation: 0.160
- Prediction Error: 0.0000
- Surprise: 0.0000
- Consolidation Count: 0