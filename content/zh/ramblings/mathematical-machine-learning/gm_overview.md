---
title: Overview of Generative Modelling
linktitle: Overview
toc: true
type: docs
date: "2019-05-05T00:00:00+01:00"
draft: false
menu:
  mathematical-machine-learning:
    parent: 2) Generative Models
    weight: 2

# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 2
---
Suppose $(\mathsf{Y}, \mathcal{Y}, \nu)$ is a probability space, and we consider $\nu$ as being the "data" distribution. Let $Y_1, \ldots, Y_n$ be IID random variables taking values in $\mathsf{Y}$ and being distributed according to $\nu$, which we consider as our observed dataset. The task of generative modelling is to learn an approximation of $\nu$ such that we can sample from it and generate realistic samples.
