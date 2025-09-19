---
title: Overview of Causal Machine Learning
linktitle: Overview
toc: true
type: docs
date: "2019-05-05T00:00:00+01:00"
draft: false
menu:
  mathematical-machine-learning:
    parent: 3) Causal ML
    weight: 6

# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 6
---
These are the major frameworks of Causal Machine Learning:

1. Neyman–Rubin causal models: Potential outcomes framework.
2. Structural Causal Models
3. Difference-in-Differences
4. Instrumental Variables

Below is a very brief overview of each of these.


## Nayman-Rubin Causal Model
Introductory Causal Example by Wikipedia: 

> "A person at age 40 would have a certain income if they had attended college, and it would have another income if they did not attend college. To measure the causal effect of going to college for this person, we need to compare the outcome for the same individual in both alternative futures. This is impossible to do and known as the fundamental problem of causal inference."

The person is known as **unit**, going to college is the **treatment** and not going to college is the **control**. Since we can't observe both alternative futures, *unit-level* causal effects cannot be observed. However, in **randomized experiments** one can **estimate** *population-level* causal effects. The average causal effect is known as **Average Treatment Effect** (ATE) is typically computed as the difference in means between the treatment and control samples. 

Randomized experiments are rare in practice and the assignments of units to treatment or control is typically non-random. In such cases, one option is to use **propensity score matching**: correct for the assignment by finding control units similar to treatment units.

> **No Causation Without Manipulation**: Suppose that units can *never*, not even in theory, receive a different treatment than the one observed. In that case it does not make sense to investigate the effect of that treatment, and we say that we can't perform causal inference without the possibility for manipulation (i.e. giving a different treatment, in theory).

## Structural Causal Models
