---
type: mechanism
id: "mechanism:incremental_predictive_coding"
activation: 1.000
prediction_error: 0.0003
surprise: 0.0000
consolidation_count: 0
tags: [mechanism]
---

# Incremental Predictive Coding

Modified PC algorithm where weight updates happen incrementally during settling rather than after full convergence. Computation time no longer scales with depth.

## Components
- incremental updates
- parallel settling
- depth-independent learning


## Connections

### Incoming
- uses_method: [[Paper - Incremental Predictive Coding A Parallel and Fully Automatic Learning Algorithm]] (w=1.00)
### Outgoing
- extends: [[Mechanism - Predictive Coding]] (w=1.00)

## Substrate State
- Activation: 1.000
- Prediction Error: 0.0003
- Surprise: 0.0000
- Consolidation Count: 0