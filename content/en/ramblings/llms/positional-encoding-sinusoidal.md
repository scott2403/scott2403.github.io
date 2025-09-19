---
title: Sinusoidal
linktitle: Sinusoidal
toc: true
type: docs
date: "2019-05-05T00:00:00+01:00"
draft: false
menu:
  llms:
    parent: 2) Positional Encoding
    weight: 4

# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 4
---
The sinusoidal positional encoding was introduced in the original [Transformer](https://arxiv.org/pdf/1706.03762) paper. It is a **deterministic**, it is not learned and encodes **absolute positions** of tokens within the sequence. In the paper, they compare it with learned PE and find identical results, but hypothesize that sinusoidal encoding might:
1. learn to attend to *relative* positions
2. extrapolate to sequence lengths longer than the ones found during training.

Suppose that we have an input sequence of length $1\leq n\leq \text{context-length}$ (recall that sequences can be smaller but not larger than the context length. They can be smaller because we construct sub-examples from each sequence). 

Suppose $\text{pos}\in\{0, \ldots, n\}$ is the index of a token within the sequence, and suppose that our embedding dimension is $d_{\text{embedding}}$, this is also known as model dimension. The sinusoidal PE method encodes the position $\text{pos}$ of a token as a vector of dimension $d_{\text{embedding}}$. Each coordinate/dimension of this embedded position vector is constructed as follows:
\begin{align}
    \text{SPE}(\text{pos})_{i} = 
    \begin{cases}
        \sin\left(\frac{\text{pos}}{10\,000\^{2i/d\_{\text{embedding}}}}\right) & \text{ if } i \text{ even} \\\\
        \\\\
        \cos\left(\frac{\text{pos}}{10\,000\^{2i/d\_{\text{embedding}}}}\right) & \text{ if } i \text{ odd}
    \end{cases}, \qquad i = 0, \ldots, d-1,
\end{align}
where I am using Python's zero-index convention. [This blogpost](https://kazemnejad.com/blog/transformer_architecture_positional_encoding/) is handy to understand sinusoidal PE.



