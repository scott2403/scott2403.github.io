---
title: Objective Function and Update Equations
linktitle: Example 1 - Objective
toc: true
type: docs
date: "2019-05-05T00:00:00+01:00"
draft: false
menu:
  normalizing-flows:
    parent: 1) Building an Intuition
    weight: 2

# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 2
---

## Data Log-Likelihood
Of course, we know what $p(x)$ looks like, it's just the pdf of a normal distribution. However, in practice, this could be a very complicated density and in that case, we can use the [univariate change of variable formula](https://en.wikipedia.org/wiki/Probability_density_function#Scalar_to_scalar) to find its pdf

$$
\log p(x) = \log p(g^{-1}(x)) + \log \left| \frac{\partial}{\partial x} g^{-1}(x)\right|.
$$

The first term can be found by using the pdf for a standard normal distribution
$$
\log p(z) = -\frac{1}{2}\log(2\pi) - \frac{z^2}{2}
$$
and plugging in the inverse transformation $g^{-1}(x) = x - \mu$
$$
\log p(g^{-1}(x)) = -\frac{1}{2}\log(2\pi) - \frac{(x-\mu)^2}{2}.
$$
For the second term, we need to compute the derivative of $g^{-1}(x)$ with respect to $x$ 
$$
\frac{\partial}{\partial x} g^{-1}(x) = 1
$$
and remember that $\log(1) = 0$ to obtain:

$$
\begin{align}
\log p(x) 
&= -\frac{1}{2}\log(2\pi) -\frac{(x-\mu)^2}{2}.
\end{align}
$$

Since the samples are i.i.d. we can estimate the log-likelihood very easily. 
$$
\begin{align}
\sum_{i=1}^n \log p(x^{(i)}) 
&= -\frac{n}{2}\log(2\pi) - \frac{1}{2}\sum^n\_{i=1} (x^{(i)} - \mu)^2
\end{align}
$$

Our objective function to minimize is then the negative log-likelihood. Often one uses the negative **average** log-likelihood instead, because this leads to more consistent gradients across different dataset sizes, you can read more about it [here](https://stats.stackexchange.com/questions/267847/motivation-for-average-log-likelihood)

$$
\frac{1}{n}\sum_{i=1}^n \log p(x^{(i)}) = -\frac{1}{2}\log(2\pi) - \frac{1}{2n}\sum^n\_{i=1} (x^{(i)} - \mu)^2
$$

## Log-Likelihood Gradient Estimates
We can now compute the gradient of the negative average log-likelihood with respect to $\mu$ 

$$
\begin{align}
  \frac{\partial}{\partial \mu} \frac{1}{n}\sum_{i=1}^n \log p(x^{(i)}) &= \frac{1}{n}\sum^n\_{i=1}x^{(i)} - \mu
\end{align}
$$

These can be easily computed since we know $n$, and $\mu$ so we compute $x^{(i)} = z^{(i)} + \mu$.

## Mean Update Equation
We can then update our mean using gradient descent with step size $\gamma$:
$$
\begin{align}
\mu_t \quad &\longleftarrow \mu\_{t-1} - \gamma \left\[\frac{1}{n}\sum^n\_{i=1}x^{(i)} - \mu\_{t-1}\right\]
\end{align}
$$

