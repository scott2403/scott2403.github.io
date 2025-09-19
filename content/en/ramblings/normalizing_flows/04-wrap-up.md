---
title: Putting it all together
linktitle: Example 1 - Wrap Up
toc: true
type: docs
date: "2019-05-05T00:00:00+01:00"
draft: false
menu:
  normalizing-flows:
    parent: 1) Building an Intuition
    weight: 4

# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 4
---

## Normalizing Flows as Maximum Likelihood Estimation

At its most basic level, Normalizing Flows can be seen as a parametric density estimation method based on Maximum Likelihood. In our case, we took a simple density $p(z) = \mathcal{N}(0, 1)$ and we fed it through a simple transformation consisting only of a linear shift $g(z) = z + \mu$. We then found the optimal parameter value $\mu$ by maximizing the likelihood of the data using gradient descent. 

Shifting a standard normal distribution $p(z) = \mathcal{N}(0, 1)$ by a constant term $\mu$ gives a new normal distribution $p(x; \mu)$ with shifted mean $p(x; \mu) = \mathcal{N}(\mu, 1)$. What we did then was minimizing (with respect to $\mu$) the KL divergence between the true data distribution that generated the data $p\_{\text{data}}(x) = \mathcal{N}(2, 1)$ and the parametrized model $p(x; \mu) = \mathcal{N}(\mu, 1)$.  

$$
\widehat{\mu} = \arg\min\_{\mu} \text{KL}(p\_{\text{data}}(x) \parallel p(x; \mu))
$$

Since minimizing the KL divergence between the true data distribution and a model is [equivalent](https://www.jessicayung.com/maximum-likelihood-as-minimising-kl-divergence/) to maximum likelihood estimation

$$
\begin{align}
  \widehat{\mu} 
  &= \arg\min\_{\mu} \text{KL}(p\_{\text{data}}(x) \parallel p(x; \mu)) \newline
  &= \arg\min\_{\mu} \mathbb{E}_{p\_{\text{data}}(x)}\left\[\log p\_{\text{data}}(x) - \log p(x; \mu)\right\] \newline
  &= \arg \max\_{\mu} \mathbb{E}\_{p\_{\text{data}}(x)}\left\[\log p(x; \mu)\right\] \newline
  &= \arg \max\_{\mu} \frac{1}{n} \sum\_{i=1}^n \log p(x^{(i)}; \mu) \qquad\qquad\qquad x^{(i)} \overset{\text{i.i.d.}}{\sim} p\_{\text{data}}(x) 
\end{align}
$$

and since the likelihood is tractable thanks to the change of variable formula, we can essentially fit $\mu$ by maximum likelihood. 


---


## Normalizing Flows in the General Univariate Case
Now that we have build some sort of intuition for normalizing flows, let's put everything together. Suppose that we have i.i.d. samples from a data distribution 
$$x^{(1)}, \ldots, x^{(n)} \overset{\text{i.i.d.}}{\sim} p\_{\text{data}}(x)$$
and that our aim is to estimate such distribution with another distribution $p(x)$. 

The Normalizing Flows method starts with a simple density $p(z)$ for which we know its analytical form, for instance a standard normal distribution $p(z) = \mathcal{N}(0, 1)$. Then, it defines a one-to-one differentiable transformation $g\_{\theta}(z)$ depending on parameters $\theta$ that maps samples from this simple distribution to new samples following a distribution given by the change of variable formula
$$
\log p(x; \theta) = \log p(g^{-1}\_{\theta}(x)) + \log \left|\frac{\partial}{\partial x} g^{-1}\_{\theta}(x)\right|.
$$

Then, finds the parameter values that maximize the likelihood, for instance using gradient descent iterations

$$
\theta\_{t} \longleftarrow \theta\_{t-1} + \gamma \frac{\partial}{\partial \theta\_{t-1}} \log p(x; \theta\_{t-1}).
$$
