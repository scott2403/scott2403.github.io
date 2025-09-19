---
title: "Black-Box Access is Insufficient for Rigorous AI Audits"
linktitle: 
toc: true
type: docs
date: "2024-06-04T00:00:00+01:00"
draft: false
menu:
  ai-safety:
    parent: 1) Governance
    weight: 3

# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 6
---
AI Auditing can happen in 4 ways:
  - Black box: Can design an input, query the system and analyze output.
  - Grey box: Limited access to inner workings (e.g. embeddings, activations, logits). 
  - White box: Full access to the system (e.g. weights, gradients, and fine-tuning)
  - Outside-the-box: Info about dev and deployment of the system (e.g. docs, data, code)
  
  Limitations of black-box access include: limited understanding of the model, components can't be studied separately, can produce misleading results (e.g. affected by Auditor bias). Here is a cool table describing the type of attacks/evaluation techniques that can be done by Auditors with different access. In Table 2 of the paper they list various ways in which white-box access allows for more thorough and safer auditing since white-box attacks are often a superset of black-box attacks and there is an order relation: black-box attacks are just inefficient white-box attacks.
  ![auditing](/table_audit.png)
