---
title: Task Set-Up
linktitle: Example 1 -  Set-Up
toc: true
type: docs
date: "2019-05-05T00:00:00+01:00"
draft: false
menu:
  normalizing-flows:
    parent: 1) Building an Intuition
    weight: 1

# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 1
---
In this Chapter, we will work through a very simple task and then see how this relates to normalizing flows.

--- 

Suppose that the random variable $x$ follows a $\mathcal{N}(\mu, 1)$

$$
\begin{equation}
p(x) = \frac{1}{\sqrt{2\pi}} \exp\left\\{-\frac{(x - \mu)^2}{2}\right\\} 
\end{equation}
$$

and that we want to generate samples from it by first sampling from a standard normal distribution and then shifting such samples accordingly

$$
\begin{align}
z^{(i)} &\sim \mathcal{N}(0, 1) \newline \newline
x^{(i)} &= z^{(i)} + \mu.
\end{align}
$$

In order to apply this transformation $g(z) = z + \mu$ we need to know the parameter $\mu$. We can find such parameter value from i.i.d. data samples $x^{(1)}, \ldots, x^{(n)} \sim \mathcal{N}(\mu, 1)$ using **Normalizing Flows**. Here is how we will proceed: 

1. Initialize our mean randomly, for instance to $\mu=-1.0$ (keeping $\sigma=1.0$). 
2. Get $n$ i.i.d. samples from the standard normal distribution $z^{(1)}, \ldots, z^{(n)}\sim \mathcal{N}(0, 1)$.
3. Use the samples to compute an estimates of the gradients of the log-pdf for $x$ with respect to $\mu$.
4. Use the gradient estimates to improve your mean estimate.

