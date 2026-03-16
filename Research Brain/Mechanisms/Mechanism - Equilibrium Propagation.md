---
type: mechanism
id: "mechanism:equilibrium_propagation"
activation: 0.160
prediction_error: 0.0001
surprise: 0.0000
consolidation_count: 0
tags: [mechanism]
---

# Equilibrium Propagation

Energy-based model that learns by contrasting free-running equilibrium with a weakly clamped phase. Computes exact gradients using only local Hebbian updates at equilibrium.

## Components
- free phase
- clamped phase
- energy function
- contrastive Hebbian update


## Connections

### Incoming
- uses_method: [[Paper - Improving Equilibrium Propagation Without Weight Symmetry Through Jacobian Homeostasis]] (w=1.00)
- uses_method: [[Paper - Unifying Predictive Coding, Equilibrium Propagation, and Contrastive Hebbian Learning]] (w=1.00)
- uses_method: [[Paper - Scaling Equilibrium Propagation to Deeper Architectures]] (w=1.10)
- uses_method: [[Paper - Equilibrium Propagation Bridging the Gap Between Energy-Based Models and Backpropagation]] (w=1.00)
- related_to: [[Concept - energy-based policy]] (w=1.00)
- related_to: [[Mechanism - Predictive Coding]] (w=1.00)
- related_to: [[Paper - Textual Equilibrium Propagation for Deep Compound AI Systems]] (w=1.00)
- uses_method: [[Paper - Scaling Equilibrium Propagation to Deep ConvNets]] (w=1.00)
- extends: [[Mechanism - Holomorphic Equilibrium Propagation]] (w=1.00)
- uses_method: [[Paper - Holomorphic Equilibrium Propagation Computes Exact Gradients Through Finite Size Oscillations]] (w=1.00)
### Outgoing
- extends: [[Mechanism - Hebbian Learning]] (w=1.00)

## Substrate State
- Activation: 0.160
- Prediction Error: 0.0001
- Surprise: 0.0000
- Consolidation Count: 0