---
title: Principal Component Analysis
linktitle: Main Ideas
toc: true
type: docs
date: "2019-05-05T00:00:00+01:00"
draft: false
menu:
  mathematical-machine-learning:
    parent: 5) PCA
    weight: 17

# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 17
---
1. Center the features values by subtracting their mean.
2. Fit a line (through the origin). Goodness-of-fit (two equivalent options):
    - Project points onto line and try to find the line that minimizes residuals.
    - Project points onto line and try to find line such that projections are furthest away from origin (sum of squared distances of all points).
   These two are equivalent but the second option is easier. Therefore **PCA** finds the best line by maximising the sum of squared distances from the projected points to the origin. This line is called **PC1** or principal component. The unit-vector in that direction is called **singular vector** or **eigenvector** for PC1. The coefficients are called **loading scores**.


## T-SNE

1. Determine similarity of all points in the scatter plot. Do this by placing a gaussian at the current point and compute the gaussian density at the value of the distance from that point. The variance of the Gaussian is determined by the density of the data near the point of interest. More dense regions have more peaked gaussians, less dense have more diffuse ones.
2. Scale the similarities from a certain point to all the others, so that they sum up to one.

Since similarity scores are not symmetric (due to different densities), the scores are simply averaged for the pair.
You end up with a similarity matrix. 

Then, project all points onto the number line. Then pick a point, and compute the distance, but this time use t-distribution rather than normal. Compute unscaled similarity scores, and then scale them. The aim of t-SNE is to make the row of the new similarity matrix (which will be messy) more similar to the corresponding row of the other matrix. At each step, it takes a small step making the rows closer.
