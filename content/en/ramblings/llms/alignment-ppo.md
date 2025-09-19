---
title: Proximal Policy Optimization
linktitle: PPO
toc: true
type: docs
date: "2019-05-05T00:00:00+01:00"
draft: false
menu:
  llms:
    parent: 6) Alignment
    weight: 17

# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 17
---
We have a SFT baseline model $\pi_0$, a policy $\pi_\theta$ and a trained reward model $r_\phi$. [**Proximal Policy Optimization**](https://arxiv.org/abs/1707.06347) was initially used in the context of RLHF by the authors of [InstructGPT](https://arxiv.org/abs/2203.02155). It consists in finding the parameters $\theta\^\*$ by **maximizing expected reward** with a **KL-regularization**
$$
\max_{\theta} \left\\{\mathbb{E}\_{\pi_\theta}\left[r_\phi(X, Y)\right] - \beta\text{KL}(\pi_\theta \parallel \pi_0)\right\\}
$$
In the original PPO paper, they mention a simple adaptivity scheme to choose $\beta$ on the fly, and it seems to be learned very quickly, so it is typically not a problem. The authors of InstructGPT mostly experimented with another version called **PPO-ptx** with a slightly modified loss.
