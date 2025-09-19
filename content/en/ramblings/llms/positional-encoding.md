---
title: Positional Encoding
linktitle: Overview
toc: true
type: docs
date: "2019-05-05T00:00:00+01:00"
draft: false
menu:
  llms:
    parent: 2) Positional Encoding
    weight: 3

# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 3
---
The attention mechanism is order-invariant. Positional Encodings (PE) are used to inject positional information of the tokens (within the context) into the Transformer architecture. Typically, positional encoding are vectors of the same dimension as the embedding vectors, and they get **added** to them.

PE can be *absolute* or *relative*, thus encoding either the absolute position of a token within a context or the position of a token relative to others. PE can be *deterministic* or *learned*, thus either being a user-specified function or be initialized as random vectors and then learned during training.

|       | Absolute | Relative |
|-------|----------|----------|
| **Deterministic** |    Sinusoidal PE     |         |
| **Learned**       |    [Convolutional Sequence to Sequence Learning](https://arxiv.org/pdf/1705.03122)     |    [Self-Attention with Relative Position Representations](https://arxiv.org/pdf/1803.02155)  and [Music Transformer](https://arxiv.org/abs/1809.04281)|

Additionally, CoPE uses context-dependent relative PE.
