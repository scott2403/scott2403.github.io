---
title: CoPE
linktitle: CoPE
toc: true
type: docs
date: "2019-05-05T00:00:00+01:00"
draft: false
menu:
  llms:
    parent: 2) Positional Encoding
    weight: 7

# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 7
---
[Contextual Positional Embedding](https://arxiv.org/abs/2405.18719) computes context-dependent relative position values and it does so by modifying the attention mechanism. It modifies the attention mechanism in the same way as relative PE, i.e. the $(i, j)$ entry of the attention matrix becomes
$$
a\_{ij} = \text{softmax}\left(\mathbf{q}\_i^\top(\mathbf{k}\_j + W_P[p_{ij}])\right),
$$
where $W_P$ is a position embedding matrix of shape $(n_{\text{embedding}}, \text{context-lenght})$, $p_{ij}$ is a "fractional" index. Typically $p_{ij}$ is an integer and so one plucks out a column of $W_P$. Here, however $W_P[p_{ij}])$ is a vector that is an interpolation between columns of $W_P$.

The position of token $j$ with respect to token $i$, where $j < i$ is computed as follow. For every intermediate token $j\leq k \leq i$ we compute $g\_{ik} = \sigma(\mathbf{q}\_i^\top \mathbf{k}\_k)$, which tells us "how much" token $k$ will be counted in measuring the position of token $j$ relative to token $i$, so that we can have fractional values that depend on context. Overall the context-dependent position value is
$$
p_{ij} = \sum_{k=j}^i g_{ik}
$$
In, say, relative PE the value $p_{ij}$ would simply be $i - j + 1$ and take integer values, and we would use these fractional values to "pluck out" the column of a position embedding matrix $W_P$ of size $(n_{\text{embedding}}, \text{context-lenght})$. Here instead we have fractional value, so we cannot index $W_P$. Therefore we be an interpolation of the relevant columns
$$
W_P[:, p_{ij}] = \left(p_{ij} - \lfloor p_{ij}\rfloor\right) W_P[\lceil p_{ij}\rceil] + (1 - p_{ij} - \lfloor p_{ij} \rfloor) W_P[\lfloor p_{ij}\rfloor]
$$

## Nuances

1. Each head will do CoPE independently.
2. $p_{ij}$ maximum value is $\text{context-length}$, but it is possible to clip it $p_{ij} = \min(p_{ij}, p_{\text{max}})$, this is chosen small in their experiments.

