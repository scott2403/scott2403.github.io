---
title: Decision Trees
linktitle: Pruning Regression Trees
toc: true
type: docs
date: "2019-05-05T00:00:00+01:00"
draft: false
menu:
  mathematical-machine-learning:
    parent: 5) Decision Trees
    weight: 13

# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 13
---
We focus on **Cost Complexity Pruning** (also known as **Weakest Link Pruning**). 

> Pruning is simply removing some of the leafs that might be overfitting, and for the leaf node we use the average response of the larger number of observations.

## Cost Complexity Pruning
One could keep on pruning until we get a tree with the single root node as the only node. There are many possible pruned sub-trees. How do we pick one? 

1. Compute the **sum of squared residuals (SSR)** for each sub-tree including the original un-pruned tree. That is we sum together the SSR for each leaf node obtaining a total SSR. Of course, each time we remove leaves, the SSR becomes larger and larger.
2. To assess the quality of the trees compute the **Tree Score** which is the sum of SSR and a **Tree Complexity Penalty**:
$$
\text{Tree Score} = \text{SSR} + \alpha T
$$
where $T$ is the number of leaf nodes in the tree and $\alpha$ is a tuning parameter found using **cross-validation**.
3. Pick the sub-tree with the lowest Tree Score.

## Cost Complexity Pruning Realistically

1. Using all the data, build a full-sized regression tree. This is fit to **all of the data** both training and testing data. This will be the "optimal tree" when $\alpha=0$.
2. Increase $\alpha$ until pruning leaves will give us a tree with a lower Tree Score. Different values of $\alpha$ will give us a sequence of sub-trees, from full-sized to just a leaf.
3. Divide data into **training** and **testing**.
4. Using **only training data**, build a sequence of sub-trees with the $\alpha$ values found before.
5. Compute the SSR of each sub-tree using **only the testing data**.
6. Go back and create new training and testing data. Keep repeating this until you've completed some form of cross-validation.

The value of $\alpha$ that **on average** (across the folds) gives us the lowest SSR for the testing data is our final value for $\alpha$. The corresponding sub-tree will be the pruned sub-tree that we selected.
