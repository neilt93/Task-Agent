---
type: mechanism
id: "mechanism:self-contrastive_forward-forward"
activation: 1.000
prediction_error: 0.0002
surprise: 0.0000
consolidation_count: 0
tags: [mechanism]
---

# Self-Contrastive Forward-Forward

Unsupervised variant of FF using contrastive learning. Positive samples from same sequence, negative from different. Extends FF to sequential and time-series tasks.

## Components
- contrastive pairs
- goodness function
- unsupervised learning
- temporal sequences


## Connections

### Incoming
- uses_method: [[Paper - Self-Contrastive Forward-Forward Algorithm]] (w=1.00)
### Outgoing
- extends: [[Mechanism - Forward-Forward Algorithm]] (w=1.00)

## Substrate State
- Activation: 1.000
- Prediction Error: 0.0002
- Surprise: 0.0000
- Consolidation Count: 0