---
title: What is Approximate Bayesian Computation?
linktitle: What is ABC?
toc: true
type: docs
date: "2019-05-05T00:00:00+01:00"
draft: false
menu:
  abc:
    parent: 1) Foundations of ABC
    weight: 1

# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 1
---
$\newcommand{\ystar}{y^{\*}}
\newcommand{\Ycal}{\mathcal{Y}}
\newcommand{\isample}{^{(i)}}$

### The Goal of ABC
The goal in ABC is to be able to obtain samples from the **parameter posterior density** given some observed data $y^*$

$$
p(\theta \mid \ystar) = \frac{p(\ystar \mid \theta) p(\theta)}{p(\ystar)}  
$$ 

when the likelihoode $p(\ystar \mid \theta)$ is **intractable**. By intractable we mean that 

- even if we are given a pair $(\theta, \ystar)$ we cannot _evaluate_ the likelihood function $p(\ystar\mid \theta)$
- we can however _simulate_ from the likelihood, i.e. given a set of parameters $\theta$ we can produce an observation $y \sim p(y \mid \theta)$

<img src="/simulator.png" alt="Simulator - Approximate Bayesian Computation" width="700"/>

For this reason, Approximate Bayesian Computation is regarded as **simulator-based inference**. We can think of the likelihood like a black box that takes in a parameter value $\theta$, it does some random calculations and outputs some data $y$. Notice that this blackbox is _not_ a deterministic function. Inside the black-box there is stochasticity.

### A bit about notation
Generally, we use the symbols $\ystar$ and $y$ to indicate two slightly different concepts. The symbol $\ystar$ denotes the actual observation that we have obtained experimentally. This is the true data that we have. The symbol $y$ denotes any data that has been simulated from the likelihood. Generally I will try my best to be consistent but sometimes I might use sloppy notation. 

Another point of confusion is whether $\ystar$ denotes _one_ observation or _many_ observations. The answer is that it depends on your assumptions and on your specific applications. In practice, I noticed that if the simulator outputs something that belongs to e.g. $(1, 2.5)^\top\in \mathbb{R}^2$ then the observation will be $\ystar = (1, 2.5)^\top$. That is, we gather together everything that gets spit out of the simulator on a single run and call that $\ystar$.

### The key idea
Okay so we have a likelihood that we cannot evaluate but that we can simulate from. How can we get samples $\theta^{(1)},\ldots, \theta^{(N)}$ from the posterior $p(\theta \mid \ystar)$? See the problem is that we can only simulate parameters from the prior $\theta \sim p(\theta)$ (since we have chosen the prior, we might as well choose one we can sample from) or data from the simulator/likelihood $y \sim p(y \mid \theta)$. However, we don't have any obvious way of simulating data from the posterior density $\theta\sim p(\theta\mid \ystar)$ indeed one of our assumptions is that we literally cannot do that _directly_.

Instead, we can come up with some indirect method. If you sample a parameter from the prior $\theta\isample \sim p(\theta)$ then this has no information about the true observed data $\ystar$. Indeed the key difference between the prior $p(\theta)$ and the posterior $p(\theta \mid \ystar)$ is that the latter contains information about the data $\ystar$ thanks to the likelihood $p(\ystar \mid \theta)$. Since the likelihood is the only thing containing information about the data $\ystar$ we must use it somehow. One of the most elementary ways of doing that is to take our prior sample $\theta\isample$ feed it through the simulator to obtain some data from the likelihood $y\isample \sim p(y \mid \theta\isample)$. At this point $\theta\isample$ is still _just_ a sample from the prior (not the posterior) but if we are incredibly lucky then maybe $\theta\isample$ will have generated data $y\isample$ that is very similar (or even identical!) to $\ystar$. This leads to the following remark:

> If generate many parameters from the prior and use them to generate data from the likelihood the chances are that some of that generated data will be quite close to the observed data. If we now discard all those parameters that led to rubbish simulated data and only keep those that produced verisimilar data, then we are left with parameters that probably are quite similar to the true parameter. 

This is the core intuition behind an algorithm called Rejection ABC which I will talk in the subchapter "Rejection ABC".

