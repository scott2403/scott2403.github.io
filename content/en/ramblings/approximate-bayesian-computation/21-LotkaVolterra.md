---
title: Lotka-Volterra
linktitle: Lotka-Volterra
toc: true
type: docs
date: "2019-05-05T00:00:00+01:00"
draft: false
menu:
  abc:
    parent: 6) Simulators
    weight: 9

# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 9
---
$\newcommand{\ystar}{y^{\*}}
\newcommand{\Ycal}{\mathcal{Y}}
\newcommand{\isample}{^{(i)}}
\newcommand{\kernel}{p\_{\epsilon}(\ystar \mid y)}
\newcommand{\tkernel}{\tilde{p}\_{\epsilon}(\ystar \mid y)}
\newcommand{\jointABCpost}{p_\{\epsilon}(\theta, y \mid \ystar)}
\newcommand{\like}{p(y \mid \theta)}
\newcommand{\prior}{p(\theta)}
\newcommand{\truepost}{p(\theta \mid \ystar)}
\newcommand{\ABCpost}{p\_{\epsilon}(\theta \mid \ystar)}
\newcommand{\ABClike}{p\_{\epsilon}(\ystar \mid \theta)}
\newcommand{\kerneltilde}{\tilde{p}\_{\epsilon}(\ystar \mid y)}
\newcommand{\zkernel}{Z\_{\epsilon}}
\newcommand{\truelike}{p(\ystar \mid \theta)}$

### Literature
The following is all taken from Graham's PhD Thesis description (and Asymptotically Exact Inference), together with the following papers: 

- Adapting the ABC Distance Function
- Automatic Posterior Transformation 
- Fast epsilon-free inference 
- Optimization Monte Carlo

### Mathematical Set-Up
Suppose that $r$ is the prey population and $f$ is the predator population. Suppose $\theta_1, \theta_2, \theta_3, \theta_4$ are parameters. The Lotka-Volterra simulator is an Euler-discretization of the following SDE

\begin{align}
dr &= (\theta_1 r - \theta_2 r f )dt + dn_r \\newline
df &= (\theta_4 r f - \theta_3 f )dt + dn_f
\end{align}

where $n_r$ and $n_f$ are zero-mean white noise processes with variances $\sigma_r^2$ and $\sigma_f^2$ respectively. We discretize using a time step $\delta t$ an initial state $(r_0, f_0)$ and $N_s$ time points. 

### Coding
```{python}
from math import sqrt
import numpy as np
from numpy.random import normal

def LV_simulator(theta):
  # Settings
  r = f = 100
  dt = 1.0
  sigma_f = sigma_r = 1.0
  N = 50
  t1, t2, t3, t4 = theta
  # Store observations
  y = []
  # Generate white noise in advance
  nr = normal(loc=0.0, scale=sigma_r, size=N)
  nf = normal(loc=0.0, scale=sigma_f, size=N)
  
  for i in range(N):
    r = r + dt * (t1*r - t2*r*f) + sqrt(dt) * nr[i]
    f = f + dt * (t4*r*f - t3*f) + sqrt(dt) * nf[i]
    y.append(r)
    y.append(f)
  return np.array(y)
```

You can find more information at the following [Google Colab notebook](https://colab.research.google.com/drive/1mu7AGcMrT97eDx3tG3q_ELWs6zTnOzZh?usp=sharing).

### Summary Statistics
Sometimes (see Epsilon-free Inference and Graham's PhD thesis) one can use the following sufficient statistics:

- The mean of the prey population
- The mean of the predator population
- The standard deviation of the prey population (or the log variance)
- The standard deviation of the predator population (or the log variance)
- The autocorrelation coefficient of lag 1 and 2 of the prey population
- The autocorrelation coefficient of lag 1 and 2 of the predator population
- The cross correlation coefficient between the prey and the predator population.
