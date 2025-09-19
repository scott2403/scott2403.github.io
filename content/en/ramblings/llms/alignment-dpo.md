---
title: Direct Preference Optimization
linktitle: DPO
toc: true
type: docs
date: "2019-05-05T00:00:00+01:00"
draft: false
menu:
  llms:
    parent: 6) Alignment
    weight: 19

# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 19
---
From the Bayesian perspective, we know that the optimal policy is given by 
$$
\pi\^{\*}\_{\phi}(x) = \frac{1}{Z} \pi_0(x)\exp\left(r\_{\phi}(x) / \beta\right), \qquad Z = \int \pi_0(x')\exp\left(r\_{\phi}(x') / \beta\right) dx'
$$
The key insight behind [**Direct Preference Optimization**](https://arxiv.org/abs/2305.18290) is that one re-write this to express the reward function as follows
$$
r\_{\phi}(y\mid x) = \beta\log\left(\frac{\pi\_{\phi}\^{\*}(y\mid x)}{\pi\_0(y\mid x)}\right) + \beta\log Z.
$$
This is essentially just another parametrization of the reward model. Now, recall that the Bradley-Terry model depends only on the **difference in reward** and therefore plugging this parametrization (and using our policy $\pi\_\theta$ rather than the actual optimal $\pi\^{\*}\_{\phi}$) there the **normalizing constant cancels out**
$$
p\^{\*}\_{\theta}(y_w \succ y_l \mid x) = \text{sigmoid}\left(\beta\log\left(\frac{\pi\_{\theta}(y\_{w}\mid x)}{\pi\_0(y_\{w}\mid x)}\right) - \beta\log\left(\frac{\pi\_{\theta}(y\_{l}\mid x)}{\pi\_0(y_\{l}\mid x)}\right)\right)
$$
We can therefore perform **maximum likelihood** directly on the policy. Our loss function therefore becomes
$$
\mathcal{L}\_{\text{DPO}}(\theta) = -\mathbb{E}\_{(x, y_w, y_l)\sim\mathcal{D}\_{\text{preferences}}}\left[\log\left\\{\text{sigmoid}\left(\beta\log\left(\frac{\pi\_{\theta}(y\_{w}\mid x)}{\pi\_0(y_\{w}\mid x)}\right) - \beta\log\left(\frac{\pi\_{\theta}(y\_{l}\mid x)}{\pi\_0(y_\{l}\mid x)}\right)\right)\right\\}\right]
$$
