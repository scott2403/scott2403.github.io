---
title: Decision Trees
linktitle: Random Forests
toc: true
type: docs
date: "2019-05-05T00:00:00+01:00"
draft: false
menu:
  mathematical-machine-learning:
    parent: 5) Decision Trees
    weight: 14

# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 14
---
Decision trees are good because:

1. Easy to use
2. Easy to build
3. Easy to interpret

However, they are bad because they are **inaccurate** on testing data. Random Forests retain the advantages of decision trees but are much more accurate on unseen data. Steps to create a random forest:

1. **Create a bootstrapped dataset**: simply randomly select samples from the original dataset **with replacement**.
2. **Build decision tree on bootstrapped data**: *but* do this only using a random subsets of features/variables **at each step** of the decision-tree-building-process. 

We repeat this multiple times and the collection of all the resulting trees is a random forest.

## Using a random forest
**For categorical responses**: Run the data through all of the trees in the random forest and return (as random forest prediction) the category that received more "votes" from the trees.**B**ootstrapping the data + **agg**regating the results from the trees in the random forest is called **Bagging**.

## Evaluating a random forest
When creating a bootstrapped dataset, not all the observations will be picked. Observations that *were not picked* form the so-called **Out-Of-Bag Dataset**. This can be used to assess how good the tree generated from that bootstrapped dataset is at generalizing.

> Basically, we run out-of-bag data through all of the trees that were build with bootstrapped datasets not containing that observation.

We repeat this for all bootstrapped dataset and for all observations in out-of-bag datasets. We can then assess how good our random forest is by the proportion of out-of-bag samples that were correctly classified by the random forest.

> The proportion of out-of-bag samples there were *incorrectly* classified is called **Out-of-Bag Error**.

## Choosing the number of random features picked at each step
When building a tree from a bootstrapped dataset, at each step of the tree-building process we pick a subset of the features at random. How do we choose how many features we pick?

> Compare the **out-of-bag** error obtained when using only $2$ features, $3$ features, etc. We then choose the most accurate random forest.

## Missing Data 
Two types of missing data:

1. missing data in the original dataset
2. missing data in the new data (e.g. newly observed test data).

### Missing Data in Original Data 
We assume that for some observations we have the value of the response, and of some features, but not all features. The most crude approximation to find a value for a missing feature is to simply look at what the *response* was for this observation, and then compute the average value for the missing feature. This is a **crude approximation** next we **refine it**.

1. Find observations that are most similar to our.
    - Build a random forest
    - Run all of the data down the trees in the forest. 
    - Samples that ended up in the same leaf node are considered *similar*
    - Keep track of similar samples using a **proximity matrix** which has a row and a column for each observation. Each time you go through a new tree in the forest, add a $1$ whenever observations end up in the same leaf. Then divide the proximity matrix entries by the total number of trees. This gives us the **proportion of trees that put each pair of observation in the same leaf node**. 
2. As before comptue the average value for the missing features, except that now it is a **weighted** average where the weights are given by the row/column of the proximity matrix (divided by the sum of that row/col). If the feature is categorical, simply choose the category with the highest proximity.
  
### Missing data in new test data
That means that we are missing both the **reponse** and some **feature values**. We assume here that the response is binary.

1. Create two copies of the data, one for each response class.
2. Use the iterative method above (which assumed only missing features) on each of these two copies.
3. Run both copies down the trees of the forest, and see which one is correctly labelled by the trees the most times.
    
## Clustering
Compute the **distance matrix** $1 - \text{ProximityMatrix}$. We can use this to show a **heatmap** that shows us how related the samples are.


