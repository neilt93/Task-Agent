---
type: mechanism
id: "mechanism:predictive_coding"
activation: 0.160
prediction_error: 0.0001
surprise: 0.0000
consolidation_count: 0
tags: [mechanism]
---

# Predictive Coding

Hierarchical generative model where each layer predicts the layer below. Learning minimizes prediction errors using only local information. Top-down predictions vs bottom-up errors.

## Components
- prediction errors
- top-down predictions
- iterative settling
- local weight updates


## Connections

### Incoming
- inspired_by: [[Mechanism - Forward-Forward Algorithm]] (w=1.00)
- uses_method: [[Paper - Prospective Configuration A Predictive Coding Solution to the Credit Assignment Problem]] (w=1.00)
- related_to: [[Paper - Predictive coding in balanced neural networks with noise, chaos and delays]] (w=1.00)
- uses_method: [[Paper - Unifying Predictive Coding, Equilibrium Propagation, and Contrastive Hebbian Learning]] (w=1.00)
- uses_method: [[Paper - The Predictive Forward-Forward Algorithm]] (w=1.00)
- uses_method: [[Paper - Incremental Predictive Coding A Parallel and Fully Automatic Learning Algorithm]] (w=1.00)
- related_to: [[Paper - AFA-PredNet The action modulation within predictive coding]] (w=1.00)
- uses_method: [[Paper - Predictive Coding Approximates Backprop Along Arbitrary Computation Graphs]] (w=1.00)
- inspired_by: [[Mechanism - JEPA (Joint Embedding Predictive Architecture)]] (w=1.00)
- uses_method: [[Paper - Scaling Predictive Coding to Deep Networks]] (w=1.00)
- related_to: [[Paper - Predictive Coding as Stimulus Avoidance in Spiking Neural Networks]] (w=1.00)
- extends: [[Mechanism - Active Inference]] (w=1.00)
- uses_method: [[Paper - Predictive Coding Beyond Gaussian Posteriors]] (w=1.00)
- extends: [[Mechanism - Incremental Predictive Coding]] (w=1.00)
### Outgoing
- related_to: [[Mechanism - Equilibrium Propagation]] (w=1.00)
- related_to: [[Mechanism - Forward-Forward Algorithm]] (w=1.00)

## Substrate State
- Activation: 0.160
- Prediction Error: 0.0001
- Surprise: 0.0000
- Consolidation Count: 0