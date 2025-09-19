---
title: Decision Trees
linktitle: Gradient Boost for Regression
toc: true
type: docs
date: "2019-05-05T00:00:00+01:00"
draft: false
menu:
  mathematical-machine-learning:
    parent: 5) Decision Trees
    weight: 16

# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 16
---
If the response is *continuous* then we are using Gradient Boost for *regression*. 

## Gradient Boost vs AdaBoost

1. *AdaBoost*: creates stumps sequentially, where each stump learns from the errors of the previous stumps by using different sample weights.
2. *GradientBoost*: starts by making a single leaf, not a tree. This leaf is a guess for the weights of all of the samples. E.g. when predicting a continuous response, this will be the average response across all observations. Then GBoost builds a tree (usually larger than a stump, but Gboost still restricts the size of the tree, typically people set the max number of leaves to $8$ or $32$). Trees are also built by learning from error of previous trees. An important difference is that Gboost **rescales all trees by the same amount**, unlike AdaBoost.

## Steps for GradientBoost

1. Compute average response. This is a leaf node.
2. Build a tree: compute **pseudo-residuals** between the average response and the responses of the various observations.
3. Build a tree to predict the residuals. We restrict the number of leaves of this tree to somewhere between $8$ and $32$. There will be typically fewer leaves than residuals. Associate each leave with the **average pseudo-residual** of the observations in that leaf.
4. To make a prediction sum the leaf node with the average response + the predicted average residual.

This creates low-bias but high-variance: **overfitting**. To avoid this, use a learning rate to multiply the predicted average residual before summing over.

5. Now create a new tree that predicts the residuals based on the "residual tree". If you want these are residuals of residuals.

Basically we repeat this many times. Contributions from all trees are scaled by the same **learning rate**.
