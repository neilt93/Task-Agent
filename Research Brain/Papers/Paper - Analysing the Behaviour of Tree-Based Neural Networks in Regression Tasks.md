---
type: paper
id: "paper:analysing_the_behaviour_of_tree-based_neural_networks_in_reg"
activation: 0.160
prediction_error: 0.0019
surprise: 0.0000
consolidation_count: 0
arxiv_id: "2406.11437v1"
published: "2024-06-17"
authors: ["Peter Samoaa", "Mehrdad Farahani", "Antonio Longa", "Philipp Leitner", "Morteza Haghir Chehreghani"]
tags: [paper]
---

# Analysing the Behaviour of Tree-Based Neural Networks in Regression Tasks

## Abstract
The landscape of deep learning has vastly expanded the frontiers of source code analysis, particularly through the utilization of structural representations such as Abstract Syntax Trees (ASTs). While these methodologies have demonstrated effectiveness in classification tasks, their efficacy in regression applications, such as execution time prediction from source code, remains underexplored. This paper endeavours to decode the behaviour of tree-based neural network models in the context of such regression challenges. We extend the application of established models--tree-based Convolutional Neural Networks (CNNs), Code2Vec, and Transformer-based methods--to predict the execution time of source code by parsing it to an AST. Our comparative analysis reveals that while these models are benchmarks in code representation, they exhibit limitations when tasked with regression. To address these deficiencies, we propose a novel dual-transformer approach that operates on both source code tokens and AST representations, employing cross-attention mechanisms to enhance interpretability between the two domains. Furthermore, we explore the adaptation of Graph Neural Networks (GNNs) to this tree-based problem, theorizing the inherent compatibility due to the graphical nature of ASTs. Empirical evaluations on real-world datasets showcase that our dual-transformer model outperforms all other tree-based neural networks and the GNN-based models. Moreover, our proposed dual transformer demonstrates remarkable adaptability and robust performance across diverse datasets.

## Key Claims
- [[Claim - This paper proposes or studies Analysing the Behaviour of Tree-Based Neural Net]]

## Methods
- [[Mechanism - a novel dual]]
- [[Mechanism - dual transformer demonstrates remarkable]]


## Connections

### Outgoing
- uses_method: [[Mechanism - a novel dual]] (w=1.00)
- authored_by: [[Author - Antonio Longa]] (w=1.00)
- authored_by: [[Author - Morteza Haghir Chehreghani]] (w=1.00)
- authored_by: [[Author - Mehrdad Farahani]] (w=1.00)
- authored_by: [[Author - Peter Samoaa]] (w=1.00)
- uses_method: [[Mechanism - dual transformer demonstrates remarkable]] (w=1.00)
- related_to: [[Concept - attention mechanism]] (w=1.00)
- related_to: [[Concept - graph neural networks]] (w=1.00)
- related_to: [[Concept - transformer architecture]] (w=1.00)
- supports: [[Claim - This paper proposes or studies Analysing the Behaviour of Tree-Based Neural Net]] (w=1.00)
- authored_by: [[Author - Philipp Leitner]] (w=1.00)

## Substrate State
- Activation: 0.160
- Prediction Error: 0.0019
- Surprise: 0.0000
- Consolidation Count: 0