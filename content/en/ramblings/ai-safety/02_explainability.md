---
title: "Interpretability Beyond Feature Attribution: Quantitative Testing with Concept Activation Vectors (TCAV)"
linktitle: 
toc: true
type: docs
date: "2024-06-04T00:00:00+01:00"
draft: false
menu:
  ai-safety:
    parent: 1) Explainability
    weight: 2

# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 1
---
Given a trained neural network, the user chooses a concepts and constructs a (relatively small) dataset of examples representing this concept, and random examples (they need not be in the training set). Then they learn a linear classifier to separate the activations produced by the trained network for these classes of examples. The normal vector to the decision boundary is the Concept Activation Vector, representing the concept. Then, the directional derivative of the output of the neural network (e.g. of the logits if it is a classification network) with respect to this CAV represents how sensitive the class prediction is to such a concept. They then perform testing to figure out if indeed a concept is related to class prediction in a significant way. 
  ![concept vectors](/concept_vectors.png)
