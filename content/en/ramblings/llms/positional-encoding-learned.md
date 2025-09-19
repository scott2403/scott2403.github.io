---
title: Learned
linktitle: Learned
toc: true
type: docs
date: "2019-05-05T00:00:00+01:00"
draft: false
menu:
  llms:
    parent: 2) Positional Encoding
    weight: 5

# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 5
---
This is the simplest method and, to my knowledge, was introduced in [Convolutional Sequence to Sequence Learning](https://arxiv.org/pdf/1705.03122). This is basically identical to embedding, except that here it is not the token index that "plucks out" a column of our matrix, but rather the "context index", i.e. the index within the context. Suppose $x$ are our tokens, then the standard embedding is a matrix $W_E$ which has shape $(n_{\text{embedding}}, n_{\text{vocab}})$ i.e. it contains a column for each vocabulary element and each of these columns have $n_{\text{embedding}}$ elements. Positional embedding, or learned positional encoding, similarly initializes a matrix $W_P$ which now has shape $(n_{\text{embedding}}, \text{context-length})$, meaning that we pluck out a column for each token within the context. Then, as usual we simply sum these two: if $w_{e}$ and $w_{p}$ are the columns of these two matrices corresponding to the same token, then we will work with $w_p + w_e$. This is also what is done in Andrej Karpathy's Makemore series videos.

> **TL;DR** We assign simply add a vector to the embedding depending on the position within the context. These vectors are parameters that are learned during training.
