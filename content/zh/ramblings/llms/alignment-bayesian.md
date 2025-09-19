---
title: Bayesian Perspective
linktitle: Bayesian Perspective
toc: true
type: docs
date: "2019-05-05T00:00:00+01:00"
draft: false
menu:
  llms:
    parent: 6) Alignment
    weight: 18

# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 18
---
The optimization problem of PPO can be rewritten from a Bayesian perspective
$$
\begin{align}
    \max_{\theta} \left\\{\mathbb{E}\_{\pi_\theta}\left[r_\phi(X, Y)\right] - \beta\text{KL}(\pi_\theta \parallel \pi_0)\right\\} 
    &= \max\_\theta \mathbb{E}\_{\pi_\theta}\left[r_\phi(X, Y) - \beta\log \pi_\theta(X) + \beta\log\pi_0(X)\right] \\\\
    &= \max\_{\theta}\mathbb{E}\_{\pi_\theta}\left[\left(\frac{r\_{\phi}(X, Y)}{\beta} + \log\pi_0(X)\right) - \beta\log\pi\_{\theta}(X)\right] \\\\
    &= \min\_{\theta} \text{KL}\left(\pi_\theta \parallel \pi\^{\*}\_{\phi}\right)
\end{align}
$$
where we have defined the distribution
$$
\pi\^{\*}\_{\phi}(x) = \frac{1}{Z} \pi_0(x)\exp\left(r\_{\phi}(x) / \beta\right), \qquad Z = \int \pi_0(x')\exp\left(r\_{\phi}(x') / \beta\right) dx'
$$
which can be interpreted as a **posterior distribution** with prior $\pi_0$ and evidence represented by the reward model $r\_{\phi}$. This Bayesian perspective was fully fleshed out in [this paper](https://arxiv.org/abs/2205.11275). Notice that then the task of performing RLHF can be cast as **variational inference** with proposal $\pi\_\theta$ and target $\pi\^{\*}_\phi$.
