---
title: "Understanding the Learning Dynamics of Alignment with Human Feedback"
linktitle: 
toc: true
type: docs
date: "2024-06-04T00:00:00+01:00"
draft: false
menu:
  ai-safety:
    parent: 1) Alignment
    weight: 2

# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 30
---
They study the learning dynamics of LLM alignment via DPO (whose optimal policy is the same as RLHF). They find that:
    1. The more "distinguishable"" the distributions of positive and negative examples are, i.e. the more disjoint their supports, the faster the loss curve decreases. In particular, they have results for $\alpha$-subexponential random variables (corresponding to the output of the network before the final unembedding layer) showing indeed how the rate of change in the unembedding weights is potentially faster for more distinguishable example and how the upper bound on the operator norm distance between un-aligned and aligned unembedding weights increases linearly with the number of steps.
    2. They find that the model tends to **prioritize** learning more distinguishable behaviores at the expense of less distinguishable ones.
    3. They also find that for an aligned model, misalignment happens faster than for an un-aligned one and motivate this by their earlier results since the aligned model already has a larger preference distinguishability. 
