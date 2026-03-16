---
type: mechanism
id: "mechanism:forward-forward_algorithm"
activation: 0.160
prediction_error: 0.0001
surprise: 0.0000
consolidation_count: 0
tags: [mechanism]
---

# Forward-Forward Algorithm

Replaces forward+backward passes with two forward passes: one with real data (positive) and one with negative data. Each layer learns to have high goodness for real data.

## Components
- positive pass
- negative pass
- goodness function
- layer-local learning


## Connections

### Incoming
- uses_method: [[Paper - The Forward-Forward Algorithm Some Preliminary Investigations]] (w=1.00)
- extends: [[Mechanism - Self-Contrastive Forward-Forward]] (w=1.00)
- related_to: [[Mechanism - Predictive Coding]] (w=1.00)
- uses_method: [[Paper - Self-Contrastive Forward-Forward Algorithm]] (w=1.00)
- uses_method: [[Paper - The Predictive Forward-Forward Algorithm]] (w=1.00)
- uses_method: [[Paper - Backpropagation-Free Spiking Neural Networks with the Forward-Forward Algorithm]] (w=1.00)
- related_to: [[Mechanism - NoProp Learning]] (w=1.00)
- uses_method: [[Paper - DeeperForward Enhanced Forward-Forward Training for Deeper and Better Performance]] (w=1.00)
### Outgoing
- inspired_by: [[Mechanism - Predictive Coding]] (w=1.00)

## Substrate State
- Activation: 0.160
- Prediction Error: 0.0001
- Surprise: 0.0000
- Consolidation Count: 0