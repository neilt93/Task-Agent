---
type: mechanism
id: "mechanism:prospective_configuration"
activation: 0.160
prediction_error: 0.0002
surprise: 0.0000
consolidation_count: 0
tags: [mechanism]
---

# Prospective Configuration

Infer neural activities first (prospective phase), then update weights to consolidate. Avoids credit assignment problem by computing targets before learning.

## Components
- activity inference
- prospective targets
- local consolidation


## Connections

### Incoming
- related_to: [[Mechanism - Generative Replay]] (w=1.00)
- uses_method: [[Paper - Prospective Configuration A Predictive Coding Solution to the Credit Assignment Problem]] (w=1.00)

## Substrate State
- Activation: 0.160
- Prediction Error: 0.0002
- Surprise: 0.0000
- Consolidation Count: 0