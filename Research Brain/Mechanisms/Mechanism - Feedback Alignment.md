---
type: mechanism
id: "mechanism:feedback_alignment"
activation: 0.160
prediction_error: 0.0001
surprise: 0.0000
consolidation_count: 0
tags: [mechanism]
---

# Feedback Alignment

Replaces backprop's symmetric weight transport with random fixed feedback weights. Networks learn despite receiving random (not transposed) error signals.

## Components
- random feedback weights
- forward weights
- asymmetric learning


## Connections

### Incoming
- uses_method: [[Paper - Direct Feedback Alignment Scales to Modern Deep Learning Tasks and Architectures]] (w=1.00)
- uses_method: [[Paper - Feedback Alignment in Deep Networks]] (w=1.00)
- uses_method: [[Paper - GrAPE Gradient Aligned Projected Error]] (w=1.00)
- related_to: [[Concept - counter-current learning]] (w=1.00)
### Outgoing
- related_to: [[Mechanism - Target Propagation]] (w=1.00)

## Substrate State
- Activation: 0.160
- Prediction Error: 0.0001
- Surprise: 0.0000
- Consolidation Count: 0