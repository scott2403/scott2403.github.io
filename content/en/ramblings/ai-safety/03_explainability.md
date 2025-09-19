---
title: "Explaining Explainability: Understanding Concept Activation Vectors"
linktitle: 
toc: true
type: docs
date: "2024-06-04T00:00:00+01:00"
draft: false
menu:
  ai-safety:
    parent: 1) Explainability
    weight: 3

# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 1
---
 They examine whether CAVs have three properties: 
  - *Consistency*: Does the perturbation of the activation $a_{\ell_1}$ by $v_{c, \ell_1}$ correspond to the perturbation of the activation $a_{\ell_2}$ by $v_{c, \ell_2}$ where $\ell_1$ and $\ell_2$ are successive layers? They find that this is typically not the case, since, they speculate, the CAVs in successive layers probably encode different aspect of that concept.
  - *Entanglement*: Different concepts can be associated - they tested cosine similarity between CAVs of associated, independent or "equivalent" concepts and find a correspondence. They find that when performing Testing with CAV (TCAV), associations can lead to misleading explanations. 
  - *Spatial Dependence*: CAVs can be dependent on the position of the concept and how this translates to the activation space. This means that it is possible to use CAVs to check if a model is translation invariant.
