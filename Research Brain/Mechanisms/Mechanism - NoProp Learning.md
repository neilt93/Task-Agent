---
type: mechanism
id: "mechanism:noprop_learning"
activation: 1.000
prediction_error: 0.0002
surprise: 0.0000
consolidation_count: 0
tags: [mechanism]
---

# NoProp Learning

Each network block independently denoises noisy labels using diffusion. No global forward or backward pass. Fully parallel, local learning.

## Components
- independent blocks
- label denoising
- diffusion-inspired
- parallel training


## Connections

### Incoming
- uses_method: [[Paper - Mono-Forward Backpropagation-Free Algorithm for Efficient Neural Network Training]] (w=1.00)
- uses_method: [[Paper - NoProp Training Neural Networks without Full Back-propagation or Full Forward-propagation]] (w=1.00)
### Outgoing
- related_to: [[Mechanism - Forward-Forward Algorithm]] (w=1.00)

## Substrate State
- Activation: 1.000
- Prediction Error: 0.0002
- Surprise: 0.0000
- Consolidation Count: 0