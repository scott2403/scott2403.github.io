---
title: ABC Densities
linktitle: ABC Densities
toc: true
type: docs
date: "2019-05-05T00:00:00+01:00"
draft: false
menu:
  abc:
    parent: 1) Foundations of ABC
    weight: 2

# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 2
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
\newcommand{\Ebb}{\mathbb{E}}$


### ABC Posterior and ABC Kernel

In the previous subsection, we realised that we need a way to compare the simulated data $y$ and the observed data $\ystar$. Suppose that we have sampled a parameter value from the prior $\theta \sim p(\theta)$ and generated an auxiliary dataset with that parameter value $y \sim p(y \mid \theta)$. We could weight the sample $(y, \theta)\sim p(y \mid \theta) p(\theta)$ by a function that measures the similarity between $y$ and $\ystar$. We call this function a *normalized* **kernel function** and we denote it by $\kernel$ where $\epsilon$ is the tolerance or bandwidth parameter. The result of this sampling and weighting operation is that we are sampling from the following density
$$
(y, \theta) \sim \jointABCpost \propto \kernel \like \prior.
$$
which we call the **augmented ABC posterior**. Of course our original aim was to sample from $\truepost$ so we need to marginalize out the auxiliary dataset $y$ to obtain an approximate parameter posterior density, which we call **ABC posterior**
$$
\ABCpost = \int\_{\Ycal} \jointABCpost dy \propto \int\_{\Ycal} \kernel \like \prior dy = \ABClike \prior.
$$
In right-hand side of the expression above we have defined the **ABC likelihood** as a convolution between the true likelihood and the normalized kernel function
$$
\ABClike = \int\_{\Ycal} \kernel \like dy.
$$
This convolution operation has a simple interpretation if you break it down: given a parameter value $\theta$ the ABC likelihood is formed as a weighted average of the true likelihood and the normalized kernel function. This operation *smoothes out* the intractable likelihood. In practice, this marginalization operation is performed simply by dropping the auxiliary dataset: the remaining $\theta$ samples will be from $\ABCpost$.


<table>
    <tr>
        <td><b>Name</b></td>
        <td><b>Notation</b></td>
    </tr>
    <tr>
        <td>Augmented ABC Posterior</td>
        <td>$\jointABCpost$</td>
    </tr>
    <tr>
        <td>ABC Posterior</td>
        <td>$\ABCpost$</td>
    </tr>
    <tr>
        <td>ABC Likelihood</td>
        <td>$\ABClike$</td>
    </tr>
    <tr>
        <td>Normalized ABC Kernel</td>
        <td>$\kernel$</td>
    </tr>
</table>



