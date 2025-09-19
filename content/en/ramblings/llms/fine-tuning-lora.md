---
title: Fine-Tuning
linktitle: LoRA
toc: true
type: docs
date: "2019-05-05T00:00:00+01:00"
draft: false
menu:
  llms:
    parent: 5) Fine-Tuning
    weight: 15

# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 15
---
In the context of Supervised Fine-Tuning (SFT) it can be very expensive to update all the parameters $\theta$, most of which are large matrices. The key idea of LoRa is that one can update these matrices using a **low-rank update**. In the paper, they freeze all parameters except the matrices $W_k$, $W_q$, $W_v$ and $W_o$ (key, query, value, output) in the MHSA layers, but in principle one could do this adaptation also on the FFNN layers.

Notice that these matrices $W$ (which we assume of shape $d\times k$) are used in a linear-fashion: an input $x\in\mathbb{R}\^k$ will be multiplied by them so that during the forward pass one would get $Wx\in\mathbb{R}\^d$. In **LoRa**, we add an extra low-rank correction which is **trainable** so that during the forward pass we compute
$$
(W + BA)x = \underbrace{Wx}\_{\text{pre-trained}} + \underbrace{BAx}\_{\text{SFT}},
$$
where $B\in\mathbb{R}\^{d\times r}$ and $A\in\mathbb{R}\^{r\times k}$ with $r \ll \min(d, k)$. The low-rank matrices are initialized with $A$ from a Gaussian and $B$ as a zero matrix. During SFT the loss function is then only over the low-rank updates, the UPT parameters are kept frozen.

$$
\min\_{\Delta} \left\\{ -\frac{1}{n\_{\text{batch}}}\sum\_{n=1}\^{n\_{\text{batch}}} \frac{1}{|y_n|} \sum\_{t=1}\^{|y_n|} \log\left(\text{softmax}(\text{LLM}\_{\theta + \Delta}(x_n, y\_{n, <t})\_{y\_{n, t}})\right) \right\\}
$$

> **Rule-of-Thumb**: Given a computational budget, it is better to use LoRa on many matrices but with small $r$ rather than LoRa on few matrices with $r$ large. They suggest updating $W_k$, $W_q$, $W_v$ and $W_o$ with a very small rank (e.g. from $1$ to $4$) and keep the rest of the parameters in the network fixed.
