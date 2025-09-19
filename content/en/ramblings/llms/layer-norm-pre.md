---
title: Pre-LN
linktitle: Pre-LN
toc: true
type: docs
date: "2019-05-05T00:00:00+01:00"
draft: false
menu:
  llms:
    parent: 3) Layer Norm
    weight: 10

# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 10
---
Nowadays LayerNorm is used *before* the important layers (MHSA/FFNN) and before the skip connection, meaning that our sequence $x$ flows through the transformations in this slightly revised order
\begin{align}
    y &= \text{MHSA}(\text{LayerNorm}(x)) + x, \\\\
    z &= \text{FFNN}(\text{LayerNorm}(y)) + y
\end{align}

## Historical Note
Apparently [Tensor2Tensor for Neural Machine Translation](https://arxiv.org/pdf/1803.07416) (16 March 2018) was the first to implement Pre-LN, but I could not find evidence of this in the paper or in their GitHub Repo. [How Much Attention Do You Need? A Granular Analysis of Neural Machine Translation Architectures](https://aclanthology.org/P18-1167.pdf) (15 July 2018) is explicit about this usage and references Tensor2Tensor. Soon after, [Generating Long Sequences with Sparse Transformers](https://arxiv.org/abs/1904.10509) (23 April 2019) used the same trick. 

The first paper to suggest this in Deep Transformers was [Adaptive Input Representations for Neural Language Modelling](https://arxiv.org/pdf/1809.10853) (22 Feb 2019), since they found it worked better empirically. [Learning Deep Transformer Models for Machine Translation](https://arxiv.org/pdf/1906.01787) (5 June 2019).

[On Layer Normalization in the Transformer Architecture](https://arxiv.org/pdf/2002.04745) (Feb 2019) was also one of the first ones to advocate for this choice and provide some motivating theory about controlling the scale of the gradients. 
