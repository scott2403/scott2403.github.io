---
title: Neyman-Rubin Potential Outcomes Framework
linktitle: Rubin Causal Model
toc: true
type: docs
date: "2019-05-05T00:00:00+01:00"
draft: false
menu:
  mathematical-machine-learning:
    parent: 3) Causal ML
    weight: 7

# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 7
---
In the potential outcomes framework we assume that there is a population of units, typically denoted by $i$, and that each unit can potentially experience two outcomes:

- If the unit is assigned to *treatment*, then it would have an outcome $Y_t(i)$ 
- If the unit is assigned to *control*, then it would have an outcome $Y_c(i)$

It is common to write either $Y_t(i)/Y_c(i)$ or $Y_1(i)/Y_0(i)$. In practice, we can only every observe one outcome for each unit, it is impossible to observe both, this is the **fundamental problem of causal inference**. 

## Unit-level Treatment Effect
For any unit $i$ in the population, its treatment effect is the difference between the outcome under treatment and the outcome under control, 
$$
\text{TE}(i) = y_t(i) - y_c(i).
$$
For $\text{TE}(i)$ to be a proper treatment effect we require some assumptions:

1. There must be a non-zero probability that unit $i$ is assigned to either the treatment or the control. If there is no possibility, then we are not in the Potential Outcomes Framework and it makes no sense to talk about Treatment Effect within the Rubin causal model.
2. All other variables, except treatment assignment, are **held constant** or they are **irrelevant**. 

The second assumption is very important: if there are variables that are not held constant between the two "hypothetical" scenarios and these variables are not irrelevant, then these variables are **confounders**. This second assumption is often known as *no causation without manipulation*.

## Average Treatment Effect (ATE)
If we could observe both alternative futures *for each unit*, then we would be able to compute the **Average Treatment Effect**
$$
\text{ATE} = \frac{1}{N_{\text{population}}} \sum_{i=1}^{N_{\text{population}}} y_t(i) - y_c(i),
$$
where $N_{\text{population}}$ is the total number of units in the population. ATE is simply the average of all unit-level treatment effects in the population. A typical assumption of the Rubin Causal Model is known as the **SUTVA assumption**: $y(i)$ is unaffected by the mechanism used to assign the treatment, and it is unaffected also by the treatment assignment of other individuals.

Of course, this is impossible to compute, however it can be estimated under certain conditions, which we will see later.

## Conditional Average Treatment Effect (CATE)
If we divide the population into subgroups (e.g. by gender, age, etc) and compute ATE for these groups, then we we obtained the **Conditional ATE**
$$
\text{CATE}(\text{subgroup}) = \frac{1}{N_{\text{subgroup}}}\sum_{i=1}^{N_{\text{subgroup}}} y_t(i) - y_c(i),
$$
where $N_{\text{subgroup}}$ is the number of units in the subgroup. As mentioned earlier, the Rubin model uses the SUTVA assumption. One way to check if the assumption holds is to divide the population into subgroups and compute CATE, if CATE is different for different subgroups, then SUTVA is violated and we say that there are **heterogeneous treatment effects**.

## Factuals and Counterfactuals
In practice, we only observe one outcome for each unit. Suppose that unit $i$ gets assigned to the treatment and therefore we only observe $Y_t(i)$. Then the observed outcome $y_t(i)$ is the **actual** outcome, whereas the outcome that we have not (and will never) observe is the **counterfactual** outcome.

## Estimating ATE

1. In a **Randomized Controlled Trial** (RCT) units are randomly assigned to treatment or control, and in this case estimating ATE is possible by simply taking the difference of the means for treatment and control units
    $$
        \widehat{\text{ATE}} = \left(\frac{1}{N_{t}}\sum_{i=1}^{N_t} y_t(i)\right) - \left(\frac{1}{N_c}\sum_{i=1}^{N_c} y_c(i)\right)
    $$
2. With **Observational Data**, if one can show (through various techniques) that the data is **as-if** coming from a RCT, then the same technique can be used.

Otherwise, it is harder to estimate ATE and there are many techniques to do so.

### Propensity Score Estimation
1. **Propensity Estimation**: Use *logistic regression* to estimate the probability of a unit belonging to treatment or control.
2. **Matching**: Match each unit in the treatment group to one (or more) unit(s) in the control group. There are multiple ways of doing this, one is based on Nearest Neighbors.
3. **Stratify**: Stratify the propensity scores into groups (e.g. $[0.0, 0.33), [0.33, 0.66), [0.66, 1.0)$) and check that covariates ara balanced within these strata.
4. **Estimate ATE**: Estimate ATE using a weighted mean within each strata.

### R-Learning
Suppose that we have available $n$ units, each having an associated $p$-dimensional vector of covariates $x_i$ which we store in a $n\times p$ matrix $X$. CATE, also known as **treatment effect heterogeneity** can be defined as
$$
\tau^\*(x) := \mathbb{E}[Y_1 - Y_0 \mid X = x]
$$
where $Y_1$ is the potential outcome subject to the treatment. One tries to learn this function $\tau^*(x)$ with a variety of ways. A popular approach is to use *meta-learners* such as S, T and R-learners. They are called meta-learners because they simply perform **regression** to learn $\tau$ and are not model-dependent. R-learning tries to learn $\tau$ **directly** compared to the other approaches.

**Treatment propensities** and **conditional response surfaces** are defined respectively as
\begin{align}
    e^*(x) &= \mathbb{P}(W = 1 \mid X = x) \\\\
    \mu_{(w)}^\*(x) &= \mathbb{E}[Y(w) \mid X = x]
\end{align}
where we assume we have access to IID and unconfoundable observations $\{(X_i, W_i, Y_i)\}$ where $X_i$ are covariates, $W_i$ is treatment assignment and $Y_i$ is the outcome. The propensities are just the distribution of the treatment assignment conditional on the covariates. The two conditional response surfaces $\mu_{(1)}^\*(x)$ and 
$\mu_{(0)}^\*(x)$ are the conditional expectation of the potential outcome corresponding to the treatment or control respectively, given $X=x$ and so we can write the CATE function as
$$
\tau^\*(x) = \mu_{(1)}^\*(x) - \mu_{(0)}^\*(x). 
$$
We also define **observation noises** 
$$
\epsilon_i(w) = Y_i(w) - \left\(\mu_{(0)}^\*(X_i) + w \tau^\*(X_i)\right).
$$
Notice that $w\in\{0, 1\}$ so when $w=0$ (control) we have $\epsilon_i(0) = Y_i(0) - \mu_{(0)}^\*(X_i)$, i.e. the noise is the difference between the potential outcome for $X_i$ corresponding to the control scenario and the expected potential outcome for the control, given $X_i$. When $w = 1$ (treatment) then we get $\epsilon_i(1) = Y_i(1) - \mu_{(1)}^\*(X_i)$ which is the same but for the treatment scenario. We also define the **conditional mean outcome**
$$
m^\*(x) = \mathbb{E}[Y \mid X = x]
$$
and we notice that by definition this can be written as follows
\begin{align}
    m^\*(x) 
    &= \mathbb{E}[Y \mid X = x] \\\\
    &= \sum_{w = 0, 1} \mathbb{E}\left[Y(w) \mid X = x\right] \mathbb{P}(W = w \mid X = x) \\\\
    &= \sum_{w = 0, 1} \mu_{(w)}^\*(x) e^\*(x) \\\\
    &= \mu_{(1)}^\*(x) e^\*(x) + \mu_{(0)}^\*(x) (1 - e^\*(x)) \\\\
    &= \mu_{(0)}^\*(x) + e^\*(x)\tau^\*(x).
\end{align}
We then define the **residuals** as the difference between the observed outcome and the conditional mean outcome
$$
r_i  = Y_i - m^\*(X_i)
$$
By using the definition of the observational noise and the derivation above we can write
\begin{align}
    r_i(W_i)
    &= \epsilon_i + \left\(\mu_{(0)}^\*(X_i) + W_i \tau^\*(X_i)\right) - \left(\mu_{(0)}^\*(X_i) + e^\*(X_i)\tau^\*(X_i)\right) \\\\
    &= \left(W_i - e^\*(X_i)\right)\tau^\*(X_i) + \epsilon_i(W_i)
\end{align}
The key to R-learning is that the CATE function satisfying the expression above can equivalently be written as the solution of this optimization problem
\begin{align}
    \tau^\* = \arg\min_\tau \mathbb{E}\left[\left((Y_i - m^\*(X_i)) - (W_i - e^\*(X_i))\tau(X_i)\right)^2\right]
\end{align}
That is, the expected squared distance between $Y_i - m^\*(X_i)$ (the difference between the observed outcome and the conditional mean outcome) and $(W_i - e^\*(X_i))\tau(X_i)$. The idea is that if one had access to the propensities and the conditional mean outcome then one could estimate the above by **empirical loss minimization** by adding a regularizer $\Lambda_n(\tau)$.

> R-learning then dives the data into strata/folds and for each fold estimates $\hat{m}$ and $\hat{e}$ and then plugs them into the empirical loss minimization version of the optimization problem above.

Suppose we have indeed estimated $\hat{m}$ and $\hat{e}$ and for now suppose that we are working on a single fold of the data. The R-learning objective becomes
$$
\min_\tau \frac{1}{n}\sum_{i=1}^n (r_i - \tau(x_i)\alpha_i)^2 + \Lambda(\tau),
$$
where
- $r_i = y_i - \hat{m}_i$ is the difference between the observed outcome and the conditional mean outcome for $x_i$
- $\alpha_i = w_i - \hat{e}_i$ is the difference between the treatment assignment and the estimated propensity for $x_i$. Typically the matrix $X_i$ will have already undergone some feature learning/transformation so one can assume a linear treatment effect
$$
\tau(x_i) = B^\top x_i.
$$


