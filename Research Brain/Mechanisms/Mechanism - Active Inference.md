---
type: mechanism
id: "mechanism:active_inference"
activation: 0.160
prediction_error: 0.0001
surprise: 0.0000
consolidation_count: 0
tags: [mechanism]
---

# Active Inference

Agents minimize variational free energy through both perception (updating beliefs) and action (changing the world). Unified framework for perception, learning, action.

## Components
- generative model
- free energy
- belief updating
- action selection


## Connections

### Incoming
- uses_method: [[Paper - Active Inference for Robot Manipulation]] (w=1.00)
- uses_method: [[Paper - Scale-Free Active Inference]] (w=1.00)
- uses_method: [[Paper - Hierarchical Active Inference Framework for Stable Robotic Control]] (w=1.00)
- related_to: [[Concept - energy-based policy]] (w=1.00)
- uses_method: [[Paper - Structural Plasticity as Active Inference]] (w=1.00)
- related_to: [[Paper - AFA-PredNet The action modulation within predictive coding]] (w=1.00)
- uses_method: [[Paper - Real-World Robot Control by Deep Active Inference with Temporally Hierarchical World Model]] (w=1.00)
- uses_method: [[Paper - Active Inference A Process Theory]] (w=1.00)
### Outgoing
- extends: [[Mechanism - Predictive Coding]] (w=1.00)

## Substrate State
- Activation: 0.160
- Prediction Error: 0.0001
- Surprise: 0.0000
- Consolidation Count: 0