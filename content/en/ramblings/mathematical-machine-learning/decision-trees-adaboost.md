---
title: Decision Trees
linktitle: AdaBoost
toc: true
type: docs
date: "2019-05-05T00:00:00+01:00"
draft: false
menu:
  mathematical-machine-learning:
    parent: 5) Decision Trees
    weight: 15

# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 15
---
Random Forest vs AdaBoost:

1. **Tree sizes**: In a RF each time you make a tree, this is full-sized. Instead, in AB trees here are typically with one root node and two leaves. These are called **stumps**. Stumps are terrible at making decisions of course, they are **weak learners**.
2. **Voting**: In RF each tree has an equal vote in the final classification. In AB some stumps get more say in the final classification.
3. **Dependence**: In RF each decision tree is made independently of the others, the order in which they were made is not important. In AB the error is important. Basically each stump is made by taking the previous stumps mistakes into account.

## Creating a forest of weak learners
1. Start by assiging to each observation/sample a **sample weight**. Initially this is uniform.
2. Make the first stump in the forest. This is done by finding the feature that does the best job at classifying the sample (assuming response is Binary). Which feature is best is calculated in terms of **Gini index** (lowest).
3. Determine how much of a "say" this stump has. The **total error** for a stump is the sum of the **sample weights** for the incorrectly-classified observations/samples. The amount of say is calculated as follows:
$$
\text{Amount of Say} = \frac{1}{2}\log\left(\frac{1 - \text{TotalError}}{\text{TotalError}}\right)
$$

In reality, after making the first stump, we need to change the weights from uniform to non-uniform. We do this by **increasing** the sample weight of the incorrectly classified observations and decreasing the others. The formula to update the weight for the incorrectly classified weights is
$$
\text{New Sample Weight} = \text{Old Sample Weight} \times e\^{\text{Amount of Say}}
$$
To decrease the sample weights for correctly classified samples we do
$$
\text{New Sample Weight} = \text{Old Sample Weight} \times e\^{-\text{Amount of Say}}
$$
After updating the sample weights, we normalize them so that they sum up to one.

One could then use these updated weights in the weighted Gini index. However, another option is to randomly replicate the data proportionally to this weights.

## Classifying with AdaBoost
Add up the amounts of says from stumps for each of the two response classes. A new observation is then assigned a reponse class based on the group with the highest amount of say.


