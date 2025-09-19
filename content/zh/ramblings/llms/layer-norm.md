---
title: Layer Norm
linktitle: Layer Norm
toc: true
type: docs
date: "2019-05-05T00:00:00+01:00"
draft: false
menu:
  llms:
    parent: 3) Layer Norm
    weight: 8

# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 8
---
Paper: [Layer Normalization](https://arxiv.org/abs/1607.06450)

Layer normalization is different from batch normalization. In layer normalization **each token** is first normalized
$$
x_{\text{normalized}} = \frac{x - \text{mean}(x)}{\text{std}(x)}
$$
and then it is shifted and scaled by learnable parameters $\gamma$ (scale) and $\beta$ (shift)
$$
\gamma x_{\text{normalized}} + \beta.
$$

The original Transformer paper used LayerNorm after the residual connection and the MSHA. However, nowadays people use LayerNorm before the MHSA and FF layers to stabilize gradients during training. 
