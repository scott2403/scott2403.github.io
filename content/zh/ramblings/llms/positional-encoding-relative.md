---
title: Relative
linktitle: Relative
toc: true
type: docs
date: "2019-05-05T00:00:00+01:00"
draft: false
menu:
  llms:
    parent: 2) Positional Encoding
    weight: 6

# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 6
---
Relative positional encoding was introduced in [Self-Attention with Relative Position Representations](https://arxiv.org/pdf/1803.02155) and then improved in [Music Transformer](https://arxiv.org/abs/1809.04281).

## Self-Attention with Relative Position Representations
They modify the self-attention mechanism to include relative positional information. Suppose the input sequence is $x = (x_1, \ldots, x_n)$, meaning that $n$ is the context length, and $x_i\in\mathbb{R}^{d_x}$. In a single head, the standard self-attention mechanism, given query, key and value matrices $W^Q$, $W^K$ and $W^V$ computes
\begin{align}
    e_{ij} &= \frac{(x_i W^Q)(x_j W^K)^\top}{\sqrt{d_z}} && \text{similarities} \\\\
    \alpha_{ij} &= \frac{\exp(e_{ij})}{\sum_{k=1}^n \exp(e_{ik})} && \text{softmax normalization} \\\\
    z_i &= \sum_{j=1}^n \alpha_{ij} (x_j W^V) && \text{attention and value multiplications}
\end{align}
They additionally learn two vectors for each pair of tokens in the context, $a_{ij}^V$ and $a_{ij}^K$. These vectors are $d_z$ dimensional and are shared across heads. Then, their new attention mechanism is
\begin{align}
    e_{ij} &= \frac{(x_i W^Q)(x_j W^K + a_{ij}^K)^\top}{\sqrt{d_z}} && \text{relative similarities} \\\\
    \alpha_{ij} &= \frac{\exp(e_{ij})}{\sum_{k=1}^n \exp(e_{ik})} && \text{softmax normalization} \\\\
    z_i &= \sum_{j=1}^n \alpha_{ij} (x_j W^V + a_{ij}^V) && \text{attention with relative value}
\end{align}
In practice, they don't learn two vectors for all possible pairs of tokens in the context. Instead, they choose a maximum "relative distance" $k$ and then clip it so that they only learn $a_{ij}^V$ and $a_{ij}^K$ from $-k$ to $k$.
