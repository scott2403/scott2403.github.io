---
title: Decision Trees
linktitle: Regression Trees
toc: true
type: docs
date: "2019-05-05T00:00:00+01:00"
draft: false
menu:
  mathematical-machine-learning:
    parent: 5) Decision Trees
    weight: 12

# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 12
---
Each leaf represent numeric values. Each leaf node represents the **average value** of the observations associated with it.

## Building a tiny tree using one feature
In a regression tree, we use the **sum of squared residuals** of the response corresponding to observations in the splits to assess the goodness of that split, rather than Gini impurity.

For a certain **numeric feature** we do as follows:

- Sort the observations based on the feature value.
- Compute the average feature value for adjacent observations to find *threhsolds*
- Compute the sum of squared residuals of the observations in the two leaves: this will. be used to assess the goodness of that specific threshold for that specific feature. The threshold with the smallest sum of residuals will be the "best" split for that feature.

Again, to avoid overfitting (low bias, high variance) we only split a feature when there are more than a minimum number of observations. Typically this number is **20**.

## Choosing a root node
If we have a bunch of predictors/features then we need to choose the root note. Simply, for each feature, we compute the best threshold, and that will become a **candidate** for the root node, and more generally for the split. We then pick the candidate with the smallest sum of squared residuals.
