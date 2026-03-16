---
type: mechanism
id: "mechanism:generative_replay"
activation: 0.160
prediction_error: 0.0001
surprise: 0.0000
consolidation_count: 0
tags: [mechanism]
---

# Generative Replay

A generative model replays pseudo-experiences from past tasks while learning new ones. Prevents catastrophic forgetting. Analogous to hippocampal replay during sleep.

## Components
- generative model
- replay buffer
- interleaved training
- consolidation


## Connections

### Incoming
- uses_method: [[Paper - Continual Learning with Deep Generative Replay]] (w=1.00)
### Outgoing
- related_to: [[Mechanism - Prospective Configuration]] (w=1.00)

## Substrate State
- Activation: 0.160
- Prediction Error: 0.0001
- Surprise: 0.0000
- Consolidation Count: 0