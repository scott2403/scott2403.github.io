---
title: Post-LN
linktitle: Post-LN
toc: true
type: docs
date: "2019-05-05T00:00:00+01:00"
draft: false
menu:
  llms:
    parent: 3) Layer Norm
    weight: 9

# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 9
---
The original Transformer paper used LayerNorm after the residual connection and the Multi-Head Self-Attention (MHSA). Suppose $x$ was our input sequence of tokens, then the sequence would go through MHSA, then added to itself and then through LayerNorm
$$
y = \text{LayerNorm}(\text{MHSA}(x) + x).
$$
This would be fed through a Feed-Forward Neural Network (FFNN) following the same proceedure
$$
z = \text{LayerNorm}(\text{FFNN}(y) + y)
$$
This is known as **Post-LN**, since the LayerNorm is done after the layer of interest (MHSA/FFNN) and importantly it is done after the residual connection. 
