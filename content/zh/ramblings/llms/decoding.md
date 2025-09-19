---
title: Decoding and Text Generation
linktitle: Random Sampling
toc: true
type: docs
date: "2019-05-05T00:00:00+01:00"
draft: false
menu:
  llms:
    parent: 4) Decoding
    weight: 11

# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 11
---
How do we generate text in practice using a Language Model? In the introduction we mentioned how one simply sequentially samples from the probability distribution corresponding to the output of the LLM and then appends the generation to the previous context and feeds this back through the network. 

While this is mostly correct, there are several **decoding** strategies that people have proposed. 

> **Decoding Strategy**: technique used to generate text from a Large Language Model.

Given a sequence $s_1, \ldots s_t$ of token indices, the output of the LLM is a vector representing the un-normalized log-probabilities of a categorical distribution over the token indices
$$
\text{LLM}_{\theta}(s_1, \ldots, s_t) = \left(\ell_1, \ldots \ell\_{n\_{\text{vocab}}}\right)^\top.
$$
For simplicity, I will write $\mathbf{p} := (p_1, \ldots, p\_{n\_{\text{vocab}}})^\top$ for the corresponding normalized probabilities, obtained by feeding the **logits** through the softmax function.

## Random Decoding
The standard strategy is to simply sample from this categorical distribution
$$
s_{t+1} \sim \text{Categorical}(p_1, \ldots, p\_{n\_{\text{vocab}}}).
$$
This can be done using a variety of methods such as **multinomial sampling**, **systematic sampling** and **stratified sampling**. These techniques are well explained in [Nicholas Chopin's "An Introduction to Sequential Monte Carlo"](https://link.springer.com/book/10.1007/978-3-030-47845-2) book. This method is not really used in practice because it can lead to very unlikely token combinations and once these have been generated, due to the autoregressive nature, it can lead in a degradation of performance. This is explained in [Hierarchical Neural Story Generation](https://arxiv.org/abs/1805.04833).

