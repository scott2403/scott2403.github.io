---
title: Generalizations
linktitle: Generalizations
toc: true
type: docs
date: "2019-05-05T00:00:00+01:00"
draft: false
menu:
  abc:
    parent: 2) Rejection and IS ABC
    weight: 6

# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 6
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
\newcommand{\truelike}{p(\ystar \mid \theta)}
\newcommand{\jointABCposttilde}{\tilde{p}\_\{\epsilon}(\theta, y \mid \ystar)}$

### Importance Sampling ABC
Consider again the Soft-ABC algorithm. Remember that we compute unnormalized weights using a kernel and then we normalize them, similar to how we do in Importance Sampling. Indeed, it turns out we can generalize Soft-ABC to something called Importance Sampling ABC or **IS-ABC**.

First of all, we need a proposal distribution. Usually one chooses
$$
q(\theta, y) = \like q(\theta)
$$
for some distribution $q(\theta)$. Then the unnormalized importance sampling weights are given by the ratio of the (unnormalized) augmented ABC posterior and the proposal distribution
$$
\tilde{w}(\theta) = \frac{\jointABCposttilde}{q(\theta, y)} = \frac{\kerneltilde \like \prior}{\like q(\theta)} = \frac{\kerneltilde \prior}{q(\theta)}
$$
The IS-ABC algorithm is given below. Importantly, notice how now we are sampling parameters from $q(\theta)$ rather than from the prior $\prior$.

<img src="/isabc.png" alt="IS-ABC - Importance Sampling ABC" width="700"/>

It is immediately clear that Soft-ABC is just a special case of this where our proposal distribution is $\like \prior$. 

You can play around with IS-ABC applied to the Two Moons example [here](https://colab.research.google.com/drive/1RCFjmtItp_-1uUP4BtlxBBAw6sWCwO94?usp=sharing). Notice that it may take a while to run. 

### Generalized Rejection ABC
One can generalize Rejection-ABC to a likelihood-free rejection sampler. The target distribution is still the augmented ABC posterior
$$
\jointABCpost = \kernel \like \prior
$$
and, as typical in rejection sampling, we require a proposal density $q(\theta, s)$ satisfying the following bound
$$
\jointABCposttilde \leq M q(\theta, s) \qquad \qquad \text{for some } M > 0
$$
One can then sample from $q(\theta, s)$ and accept draws with probability 
$$
\frac{\jointABCposttilde}{M q(\theta, s)}
$$
In particular, here we choose the following proposal distribution 
$$
q(\theta, s) = \like q(\theta)
$$
for some $q(\theta)$ satisfying the bound above. Of course, the reason of this choice of proposal distribution is so that the intractable likelihood cancels out in the acceptance probability
$$
\frac{\jointABCposttilde}{M q(\theta, s)} = \frac{\kerneltilde \like \prior}{ M\like q(\theta)} = \frac{\kerneltilde \prior}{ Q q(\theta)}
$$
The the generalized rejection-ABC algorithm is given below.

<img src="/generalizedrejectionabc.png" alt="Generalized Rejection ABC" width="700"/>

It is well-known in Rejection sampling that the optimal value for the constant $M$ is
$$
M^* = \max\_{\theta, y} \frac{\jointABCposttilde}{\like q(\theta)} = \max\_{\theta, y} \frac{\kerneltilde \prior}{q(\theta)} = \max\_{y} \kerneltilde \max\_{\theta} \frac{\prior}{q(\theta)}
$$

