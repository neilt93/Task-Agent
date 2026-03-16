---
type: mechanism
id: "mechanism:diffusion_policy"
activation: 0.160
prediction_error: 0.0001
surprise: 0.0000
consolidation_count: 0
tags: [mechanism]
---

# Diffusion Policy

Uses denoising diffusion to model multi-modal action distributions for robot control. Iteratively denoises random noise into actions conditioned on observations.

## Components
- denoising network
- noise schedule
- observation conditioning
- action generation


## Connections

### Incoming
- related_to: [[Concept - energy-based policy]] (w=1.00)
- uses_method: [[Paper - Diffusion Policy Visuomotor Policy Learning via Action Diffusion]] (w=1.00)
### Outgoing
- related_to: [[Mechanism - Central Pattern Generator]] (w=1.00)

## Substrate State
- Activation: 0.160
- Prediction Error: 0.0001
- Surprise: 0.0000
- Consolidation Count: 0