---
title: Markov Chain Monte Carlo ABC
linktitle: MCMC-ABC
toc: true
type: docs
date: "2019-05-05T00:00:00+01:00"
draft: false
menu:
  abc:
    parent: 3) MCMC ABC
    weight: 7

# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 7
---
$\newcommand{\ystar}{y^{\*}}
\newcommand{\Ycal}{\mathcal{Y}}
\newcommand{\isample}{^{(i)}}
\newcommand{\kernel}{p\_{\epsilon}(\ystar \mid y)}
\newcommand{\tkernel}{\tilde{p}\_{\epsilon}(\ystar \mid y)}
\newcommand{\jointABCpost}{p_\{\epsilon}(\theta, y \mid \ystar)}
\newcommand{\like}{p(y \mid \theta)}
\newcommand{\prior}{p(\theta)}
\newcommand{\truepost}{p(\theta \mid \ystar)}
\newcommand{\ABCpost}{p\_{\epsilon}(\theta \mid \ystar)}
\newcommand{\ABClike}{p\_{\epsilon}(\ystar \mid \theta)}
\newcommand{\kerneltilde}{\tilde{p}\_{\epsilon}(\ystar \mid y)}
\newcommand{\zkernel}{Z\_{\epsilon}}
\newcommand{\truelike}{p(\ystar \mid \theta)}$

### MCMC-ABC
Rejection ABC could work in some simple problems but would be very inefficient in high-dimensions. We would like to come up with an algorithm that is more efficient than Soft-ABC and allows us to tackle harder problems. One idea is to use Markov Chain Monte Carlo. Specifically, we use the Metropolis-Hastings algorithm to target the augmented ABC posterior
$$
\jointABCpost \propto \prior \like \kernel
$$
As for any Metropolis-Hastings we would need to specify a proposal distribution $q(y', \theta' \mid y, \theta)$ and an initialization. 

### MCMC-ABC with Hard Kernel
Suppose that we use one step of Rejection-Sampling to obtain a pair $(\theta, y) \sim \jointABCpost$. Then clearly we must have that $\mathbb{I}\_{B\_{\epsilon}(y)}(\ystar) = 1$ or otherwise the pair $(\theta, y)$ would have been rejected by the algorithm. Now we need to choose a proposal distribution for our MH algorithm. A simple choice is as follows
$$
q(y', \theta' \mid y, \theta) = p(y' \mid \theta') q(\theta' \mid \theta)
$$
which essentially means that (independently of $y$) we first sample a new parameter value $\theta' \sim q(\theta' \mid \theta)$ and then we feed it through the simulator to obtain a new dataset $y' \sim p(y' \mid \theta')$ hence obtaining $(\theta', y')$. The Metropolis-Hastings acceptance probability would then become
$$
\begin{align}
    \alpha(y', \theta' \mid y, \theta) &= \min\left\\{1, \frac{p\_{\epsilon}(\theta', y' \mid \ystar)}{\jointABCpost}\cdot \frac{q(y, \theta \mid y', \theta')}{q(y', \theta' \mid y, \theta)}\right\\} \newline 
    &= \min\left\\{1, \frac{p(\theta') p(y'\mid \theta') \mathbb{I}\_{B\_{\epsilon}(y')}(\ystar)}{\prior \like \mathbb{I}_{B\_\epsilon(y)}(\ystar)} \cdot \frac{p(y \mid \theta) q(\theta \mid \theta')}{p(y' \mid \theta') q(\theta' \mid \theta)}\right\\} \newline
    &= \min\left\\{1, \frac{p(\theta') q(\theta \mid \theta')}{p(\theta) q(\theta' \mid \theta)} \cdot \mathbb{I}\_{B\_{\epsilon}(y')}(\ystar)\right\\}
\end{align}
$$
where in the last line we have used the fact that $\mathbb{I}\_{B\_{\epsilon}(y)}(\ystar) = 1$ since the pair $(\theta, y)$ was generated using Rejection-ABC. Putting everything together we obtain the following MCMC-ABC algorithm by Majoram 2003. 

<img src="/mcmc-abc.png" alt="MCMC ABC Pseudocode - Approximate Bayesian Computation" width="700"/>

The notebook with the code for the MCMC-ABC algorithm with Hard Kernel can be found [here](https://colab.research.google.com/drive/1xPw_NTzv98eV4uWV8JjUidAm7z3v3JKH?usp=sharing).

### MCMC-ABC with Soft Kernel
Wilkinson 2008 generalizes this to the use of a soft kernel so that the acceptance probability now includes the ratio of the two kernels.

<img src="/mcmc-abc-soft.png" alt="MCMC ABC with Soft Kernel Pseudocode - Approximate Bayesian Computation" width="700"/>

The notebook with the code for the MCMC-ABC algorithm with Soft Kernel can be found [here](https://colab.research.google.com/drive/1VP4i7_idhyjlPL1nB39AOXos_o3jbG7i?usp=sharing).

The original paper is [Markov Chain Monte Carlo without likelihoods](https://www.pnas.org/content/pnas/100/26/15324.full.pdf).
