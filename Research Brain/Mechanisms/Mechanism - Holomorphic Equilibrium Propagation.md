---
type: mechanism
id: "mechanism:holomorphic_equilibrium_propagation"
activation: 1.000
prediction_error: 0.0002
surprise: 0.0000
consolidation_count: 0
tags: [mechanism]
---

# Holomorphic Equilibrium Propagation

Extends EP using complex-valued perturbations. Extracts exact gradients from the first Fourier coefficient of neuronal oscillations, eliminating the need for separate phases.

## Components
- complex perturbations
- Fourier analysis
- oscillatory dynamics
- exact gradients


## Connections

### Incoming
- uses_method: [[Paper - Improving Equilibrium Propagation Without Weight Symmetry Through Jacobian Homeostasis]] (w=1.00)
- uses_method: [[Paper - Holomorphic Equilibrium Propagation Computes Exact Gradients Through Finite Size Oscillations]] (w=1.00)
### Outgoing
- extends: [[Mechanism - Equilibrium Propagation]] (w=1.00)

## Substrate State
- Activation: 1.000
- Prediction Error: 0.0002
- Surprise: 0.0000
- Consolidation Count: 0