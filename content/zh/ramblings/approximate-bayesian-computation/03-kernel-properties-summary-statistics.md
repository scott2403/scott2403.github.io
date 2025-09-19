---
title: ABC Kernel and Summary Statistics
linktitle: ABC Kernel and Summary Statistics
toc: true
type: docs
date: "2019-05-05T00:00:00+01:00"
draft: false
menu:
  abc:
    parent: 1) Foundations of ABC
    weight: 3

# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 3
---
$\newcommand{\ystar}{y^{\*}}
\newcommand{\Ycal}{\mathcal{Y}}
\newcommand{\isample}{^{(i)}}
\newcommand{\kernel}{p\_{\epsilon}(\ystar \mid y)}
\newcommand{\jointABCpost}{p_\{\epsilon}(\theta, y \mid \ystar)}
\newcommand{\like}{p(y \mid \theta)}
\newcommand{\prior}{p(\theta)}
\newcommand{\truepost}{p(\theta \mid \ystar)}
\newcommand{\ABCpost}{p\_{\epsilon}(\theta \mid \ystar)}
\newcommand{\ABClike}{p\_{\epsilon}(\ystar \mid \theta)}
\newcommand{\kerneltilde}{\tilde{p}\_{\epsilon}(\ystar \mid y)}
\newcommand{\zkernel}{Z\_{\epsilon}}
\newcommand{\truelike}{p(\ystar \mid \theta)}$

### Summary Statistics
To alleviate the curse of dimensionality, one often prefers to work with a lower-dimensional version of the observations: **summary statistics**. A summary statistics is simply a function of the data, whose output lives in a lower-dimensional space. If summary statistics $s(y)$ are **sufficient** then targeting $p\_{\epsilon}(\theta \mid s(y))$ is equivalent to targeting the ABC posterior as there is no loss of information. Usually, it is very hard to find sufficient statistics so one settles for the best summary statistics one can find. Unless explicitly mentioned, we will not use summary statistics and instead work with the data directly.

### Kernel Properties
The ABC kernel $\kernel$ assigns a weight to a pseudo-observation $y$ based on how close it is to the true data $y$. The closer $y$ to $\ystar$, the larger the weight, the further $y$ from $\ystar$, the smaller.
We would like the kernel to satisfy two properties:

- As the tolerance gets smaller, the ABC likelihood tends to the true likelihood. In particular, we assume tha the unnormalized kernel $\kerneltilde = \zkernel \kernel$ tends to the Dirac Delta function as the tolerance $\epsilon$ goes to $0$
$$
\kerneltilde \to \delta\_{\ystar}(y)
$$
where $\zkernel$ is the normalizing constant for the kernel. Thanks to this property we can show that the ABC likelihood tends to the true likelihood (and hence the ABC posterior tends to the true posterior)
$$
\lim\_{\epsilon \to 0} \ABClike = \lim\_{\epsilon \to 0}\frac{\displaystyle \int \kerneltilde \like dy}{\displaystyle \int \kerneltilde dy} = \frac{\displaystyle \int \delta\_{\ystar}(y) \like dy}{\displaystyle \int \delta\_{\ystar}(y) dy} = \truelike
$$
- As the tolerance gets larger, the ABC likelihood gives no information and is in fact constant, so that the ABC posterior tends to the prior.
$$
\lim\_{\epsilon \to \infty} \ABCpost = 
\left[\int\_{\Ycal} \like dy \right] \prior = \prior
$$

### Summary of Approximations in ABC
There are therefore two main source of approximation in ABC: 

  - Error induced by non-sufficient summary statistics $s(y)$, required to lower the dimensionality.
  - Error induced by a positive bandwidth parameter $\epsilon > 0$ for the ABC kernel.

