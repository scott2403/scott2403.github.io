---
title: Decision Trees
linktitle: Classification Trees
toc: true
type: docs
date: "2019-05-05T00:00:00+01:00"
draft: false
menu:
  mathematical-machine-learning:
    parent: 5) Decision Trees
    weight: 11

# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 11
---
Given an $n\times p$ data array $X$ and an $n$-dimensional response vector $y$ there are two types of Decision trees:

- **Classification Tree**: $y$ is *categorical*.
- **Regression Tree**: $y$ is *numerical*.

One advantage of decision trees over other methods it's that it can natively handle different data types.

## Nomenclature

- **Root note**: top node which determines the first split/decision.
- **Leaf node**: any node from which no arrows depart.
- **Internal node/branch**: any node having both inbound and outbound arrows.

## Building a Classification Tree
Requires the following steps:

1. 
2. 
3. 

## Finding the Root Node
- *Classification Tree, Binary feature*: Create a tiny sub-tree with the binary feature as root node and two leave nodes. Go through the whole dataset and keep track of the label of the feature and the response. For instance if the feature is "likes walking" and the response is "drinks tea" then we keep track on each leaf node on the count of *True* (drinks tea) and *False* (does not drink tea). When a leaf contains a mixture of labels of the target (like here, a counterexample would be a leaf node that split the data into only *True* and only *False*) is called **impure leaf**. How to *quantify* the impurity of the leaves? One metric is **Gini Impurity**. Other methods are **Entropy** or **Information Gain**. How to compute Gini Impurity? Suppose that for a leaf in this subtree we have $|T|$ true target labels and $|F|$ false target labels. Then we compute the gini impurity for that leaf as
$$
\text{GiniLeaf} = 1 - \underbrace{\left(\frac{|T|}{|T| + |F|}\right)^2}_{\text{probability of true squared}} - \underbrace{\left(\frac{|F|}{|T|+|F|}\right)^2}\_{\text{probability of false squared}}
$$
However, each leaf does not necessarily have the same number of datapoints associated with it. Therefore the total Gini impurity for a feature is given by weighted average of the leaf gini impurities
$$
\text{GiniFeature} = \sum\_{i=1}^{2}\frac{\text{\# obs leaf i}}{\text{\# obs both leaves}}\times \text{GiniLeaf}(\text{Leaf}_i)
$$
- *Classification Tree, Numerical feature*: Sort the values of the feature. Compute the average of that feature for all **adjecient** pairs of observations. These provide us with **thresholds**. We then compute the **Gini impurity** for each of the resulting **subtrees** just like for a binary feature. Pick the **lowest** Gini impurity to decide the threshold for that feature.

To choose a feature as the root node, simply choose the split with the lowest gini impurity.

## Building Internal Nodes
Simply choose the leaf nodes of the root node that are not impure and perform recursively the same operation as before. Basically for each node, we stop if it has zero Gini impurity. The output of a Leaf is whatever category has the most values.

## Avoiding Overfitting
In theory, we should stop only whenever all leaf nodes have zero Gini impurity. However, in practice it often happens that this leads to **overfitting** because those nodes will have only one or two observations associated with them. To solve this problem:

1. **Pruning**:
2. **Limit how much trees can grow**: e.g. one can choose the minimum number of observation per leaf node. We do not build further nodes if the resulting leaves have fewer of those observations. How do you choose the number of minimum observation per leaf node? By **cross-validation**.

The value 
 


