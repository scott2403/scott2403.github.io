---
title: Two Moons
linktitle: Two Moons
toc: true
type: docs
date: "2019-05-05T00:00:00+01:00"
draft: false
menu:
  abc:
    parent: 6) Simulators
    weight: 8

# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 8
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

### Mathematical Set-Up
Given $\theta\in\mathbb{R}^2$, produces data $y\in\mathbb{R}^2$ as follows
$$
\begin{align}
    a &\sim \mathcal{U}\left(-\frac{\pi}{2}, \frac{\pi}{2}\right) \newline
    r &\sim \mathcal{N}(0.1, 0.01^2) \newline
    p &= (r\cos(a) + 0.25, r\sin(a))^\top \newline
    y &= p + \left(-\frac{|\theta_1 + \theta_2|}{\sqrt{2}}, \frac{-\theta_1 + \theta_2}{\sqrt{2}}\right)
\end{align}
$$

### Coding
```{python}
def TM_simulator(theta):
    """Two Moons simulator for ABC."""
    t0, t1 = theta[0], theta[1]
    a = uniform(low=-np.pi/2, high=np.pi/2)
    r = normal(loc=0.1, scale=0.01)
    p = np.array([r * np.cos(a) + 0.25, r * np.sin(a)])
    return p + np.array([-np.abs(t0 + t1), (-t0 + t1)]) / sqrt(2)
```

