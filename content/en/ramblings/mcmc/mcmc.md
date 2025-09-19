---
title: MCMC Literature
linktitle: MCMC Literature
toc: true
type: docs
date: "2019-05-05T00:00:00+01:00"
draft: false
menu:
  mcmc:
    parent: 1) MCMC Literature
    weight: 1

# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 1
---
$\newcommand{\ystar}{y^{\*}}
\newcommand{\Ycal}{\mathcal{Y}}
\newcommand{\isample}{^{(i)}}$
$$
\newenvironment{rcases}
  {\left.\begin{aligned}}
  {\end{aligned}\right\rbrace}
$$
### 1953 - Metropolis
Aim is to approximate the following integral (canonical ensamble)
$$
\overline{F} = \frac{\int F \exp(-E/kT) dp dq}{\int \exp(-E/kT) dpdq}
$$
This is not feasible using standard numerical methods (using a grid of points). One could choose integration points at random *uniformly* and then give these points a *weight* $\exp(-E/kT)$ however this would not be practical as most of the mass would be located in a small set. Instead we choose points with probability $\exp(-E/kT)$ and then weight them evenly. We move the point $(X, Y)$ to
$$
\begin{align}
X = X + \alpha \xi\_1 \qquad \xi_1 \sim U(-1, 1)   \newline
Y = Y + \alpha \xi\_2 \qquad \xi_2 \sim U(-1, 1)
\end{align}
$$
We then calculate the change in energy of the system $\Delta E$ and if $\Delta E < 0$ (we have moved to a region of lower energy i.e. higher probability) we accept the move. Otherwise if $\Delta E > 0$ we allow the move with probability $\exp(-\Delta E / kT)$. We then approximate the expectation as
$$
\overline{F} = \frac{1}{M} \sum\_{i=1}^M F_j
$$
The proof of correctness essentially proves that we choose points with probability $\exp(-E/kT)$. 

- Proof that the method is ergodic, i.e. we can reach any point on the domain.
- Detail balance proof

### 1970 Hastings
Features of MCMC:

- Computations depend only on target $p(x)$ via the ratio $p(x')/p(x)$ therefore normalizing constants need not be known, no factorization of $p(x)$ is necessary.
- Samples are obtained via a Markov Chain, hence are *correlated*. Therefore estimating standard deviation of our estimates of expectations require more care.

We have a probability distribution $\pi = (\pi_0, \ldots, \pi_S)$ and a function $f$ defined on the state space. We wish to estimate
$$
I = \mathbb{E}\_{\pi}[f] = \sum\_{i=0}^S f(i) \pi_i
$$
We construct $P$ so that $\pi$ is its unique stationary distribution
$$
\pi = \pi P
$$
and we can approximate $I$ with
$$
\hat{I} = \frac{1}{N} \sum\_{t=1}^{N} f(X(t)).
$$
If the Markov Chain defined by $P$ is finite and irreducible we know $\hat{I}$ is asymptotically normally distributed and $\hat{I}\to I$ in mean square as $N \to \infty$. Notice that since $X(t)$ is a Markov Chain, it only depends on the previous state, so it is asymptotically stationary (meaning that the distribution of $X$ does not change when shifted in time). Hence we can estimate the variance of this estimator using techniques for a stationary process:

$$\text{var}(\overline{Y}) = \frac{\sigma^2}{N}\sum\_{j = -N +1}^{N-1}\left(1 - \frac{|j|}{N}\right)\text{corr}(Y(t), Y(t+)) \longrightarrow 2 \pi \frac{g(0)}{N}\quad \text{as } N \to \infty$$

We construct $P$ as follows. Let $Q=(q\_{ij})$ be a transition matrix and 
$$
\begin{cases}
p\_{ij} = q\_{ij} \alpha\_{ij}   & \text{Probability of leaving $i$} \newline
p\_{ii} = 1 - \sum\_{i \neq j}p\_{ij} & \text{Probability of staying at $i$}
\end{cases}
$$
where $\alpha\_{ij}$ is the probability of accepting the proposed state $j$ from $i$ and is defined as
$$
\alpha\_{ij} = \frac{s\_{ij}}{1 + \frac{\pi\_i}{\pi\_j}\frac{q\_{ij}}{q\_{ji}}}
$$
and $s\_{ij}$ is a symmetric function of $i$ and $j$ chosen so that $\alpha\_{ij}\in[0, 1]$ for all $i,j$. In particular the Metropolis Method uses
$$
s\_{ij} = 
\begin{cases}
    1 + \frac{\pi\_i q\_{ij}}{\pi\_j q\_{ji}}& \text{if  }\frac{\pi\_jq\_{ji}}{\pi\_i q\_{ij}}\geq 1  \newline
    1 + \frac{\pi\_j q\_{ji}}{\pi\_iq\_{ij}} & \text{if  }\frac{\pi\_jq\_{ji}}{\pi\_i q\_{ij}}<1
\end{cases}
$$
and when the proposal is symmetric we get
$$
\alpha\_{ij} = \begin{cases}
     1 & \frac{\pi\_j}{\pi\_i} \geq 1 \newline
     \frac{\pi\_j}{\pi\_i} & \frac{\pi\_j}{\pi\_i} < 1
\end{cases}
$$
Hastings also produces a proof for the fixed-scan Gibbs sampler. If the dimension is $d$ then we consider the process observed at times $0, d, 2d, \ldots$. This is a Markov Process with transition $P = P_1 \cdots P_d$. As long as $\pi P_k = \pi$ for every $k$ then $\pi$ will be a stationary distribution of $P$ because
$$
\pi P = \pi P_1 \cdots P_d = \pi P_2 \cdots P_d = \cdots = \pi
$$
To make sure that the stationary distribution is unique, then we must check the irreducibility of $P$. Hastings advices:

- Choose $Q$ proposing far away points but with low rejection rate.
- Choose initial state in region of high probability, if possible.

### 1994 Tierney
#### Markov Transition Kernel
A Markov Transition Kernel on $(E, \mathcal{E})$ is a map $P:E\times \mathcal{E}\to [0, 1]$ such that

- $P(\cdot, A)$ is a measurable function, for any fixed $A\in\mathcal{E}$
- $P(x, \cdot)$ is a probability measure on $(E, \mathcal{E})$ for any fixed $x\in E$.

#### Time-homogeneous MC Kernel
We call a sequence of $E$-valued random variables $\\{X_n\\, :\\, n \geq 0\\}$  a time-homogeneous Markov Chain if the kernel has the following form
$$
P(X\_n, A) = P(X\_{n+1}\in A \mid X\_0, \ldots, X\_n).
$$
It is a Markov Chain because the probability that $X\_{n+1}\in A$ depends only on $X_n$ and not on the whole history. It is time-homogeneous because the kernel $P$ is the same used independently of time (we don't use a different kernel $P_n$ at every step $n$). 
We denote by $P_x$ probabilities for the Markov Chain with kernel $P$, started at $x$.

#### Kernel Operations
Let $\nu$ be a probability measure , $P$ a kernel on $(E, \mathcal{E})$ and $h$ be a real-valued $\mathcal{E}-$measurable function. 

- Kernel operates on the *left* with *probability measures*
$$
(\nu P)(A) = \int P(x, A) \nu(dx)
$$
- Kernel operates on the *right* with *measurable functions*
$$
(Ph)(x) = \int h(y) P(x, dy)
$$

Notice that both of the properties above are just expectations of the from
$$
\nu h = \nu[h] = \mathbb{E}\_{\nu}[h] = \int h(y) \nu(dy)
$$
In particular, $\nu P$ is the expectation with respect to $\nu$ of the measurable function $P(\cdot, A)$, i.e. $\nu P = \mathbb{E}\_{\nu}[P(x, A)]$. The second expression is the expectation of $h$ with respect to the measure $P(x, \cdot)$, i.e. $P h = \mathbb{E}\_{P(x, \cdot)}[h]$


#### Invariant Measure
We say that kernel $P$ leaves the measure $\pi$ invariant if for every measurable $A$
$$
\pi(A) = \int \pi(dx) P(x, A)
$$
Essentially this means that if we operate the kernel $P$ on the left against $\pi$ the resulting measure is still $\pi$, this is written as $\pi = \pi P$.

#### Irreducibility
Let $\pi$ be a $\sigma$-finite measure. A kernel $P$ on $(E, \mathcal{E})$ is $\pi-$irreducible if $\pi(E) > 0$ and for each point $x\in E$ and set $A\in\mathcal{E}$ with $\pi(A)>0$ I can find an integer $n=n(x, A)\geq 1$ such that $P^n(x, A) > 0$.

This means that as long as the measure $\pi$ gives positive mass to the whole of $E$ and then I can always find a number of steps that will lead me from any $x\in E$ to any $A\in\mathcal{E}$, where $A$ must have positive mass under $\pi$. Basically I can visit the whole space if you give me long enough time!

#### Periodicity
A $\pi$-irreducible kernel $P$ is periodic if there exists an integer $d\geq 2$ and a sequence $\\{E_0, E_1, \ldots, E\_{d-1}\\}$ of $d$ non-empty *disjoint* sets in $\mathcal{E}$ such that for all $i=0, \ldots, d-1$ and all $x\in E$
$$
P(x, E_j) = 1 \qquad \qquad \text{for } j = i + 1 \text{    mod } d
$$
This means that if I am at $x\in E_i$ then I will move with probability $1$ to $E_j$ in $1$ step (where $j = i + 1 \text{  mod } d$). The result of this is that we are going to visit each of $E_i$ periodically. If the kernel is not periodic, it is **aperiodic**.

#### Recurrence
Suppose $X_n$ is a Markov Chain generated by kernel $P$ which is $\pi$-irreducible (can explore the whole space) and has $\pi$ as its invariant distribution. We say that $X_n$ is recurrent if for every $B$ with positive mass $\pi(B)>0$ we have

- $P_x\\{X_n \in B \text{ i.o }\\} \qquad\qquad \text{ for all } x$
- $P_x\\{X_n \in B \text{ i.o }\\} = 1 \qquad\\, \text{ for } \pi\text{-almost all } x$

here i.o. means infinitely often. Basically the first condition says that, no matter your starting point, you have a positiev probability of visiting each set $B$ with positive mass infinitely often. The second condition tells us that if you start from a $x$ in a set of positive measure then actually you will visit each $B$ infinitely often with probability $1$. 

If $P_x\\{X_n \in B \text{ i.o }\\} = 1$ for every $x$ then we say the chain is **Harris recurrent**. Basically this means that we have probability $1$ of visiting each $B$ infinitely often, even if we start from an $x$ in a set of measure zero.

Suppose $P$ has a unique invariant distribution. We say $P$ is positive recurrent if the total mass of this measure is *finite*.

#### Unique Invariant
$$
\begin{rcases}
    P \quad\pi\text{-irreducible} \newline
    \quad \newline
    P \quad\quad\pi\text{-invariant}
\end{rcases} \implies P \text{ recurrent and } \pi \text{ unique invariant }
$$

#### Total Variation Norm
If $\lambda$ is a bounded signed measure on $(E, \mathcal{E})$ then we define the total variation norm as
$$
\parallel \lambda \parallel = \sup\_{A\in\mathcal{E}} \lambda (A) - \inf\_{A\in\mathcal{E}} \lambda(A)
$$

#### Equilibrium Distribution
Suppose that $P$ is $\pi$-irreducible and $\pi$ is its invariant distribution, i.e. $\pi=\pi P$. Then $P$ is positive recurrent and $\pi$ is the *unique* invariant measure of $P$. If $P$ is also *aperiodic* then for $\pi$-almost all $x$
$$
\parallel P^n(x, \cdot) - \pi\parallel\_{\text{TV}} \to 0
$$
If $P$ is *Harris recurrent* then the convergence occurs for all $x$. Basically this means that if $P$ can explore the whole space, has $\pi$ as its invariant distribution and is aperiodic, then $\pi$ will also be its equilibrium distribution. 

NB: Above we used $P^n(x, A) = P(X_n \in A \mid X_0 = x)$.

Importantly, the assumptions above are both necessary and sufficient. This means that if 
$$
\parallel P^n(x, \cdot) - \pi \parallel\_{\text{TV}} \to 0
$$
then the chain is $\pi$-irreducible, aperiodic, positive Harris recurrent and has invariant distribution $\pi$.

#### Harris Recurrent Checks
Let $h$ be a non-negative real-valued function. We say $h$ is *harmonic* for $P$ if $h = Ph$. 
Now let $P$ be recurrent. Then it is also *Harris recurrent* if and only if every bounded harmonic function is a constant.

Suppose $P$ is $\pi$-irreducible and $\pi P = \pi$. If the measure $P(x, \cdot)$ is absolutely continuous with respect to $\pi$ for all $x$, i.e. $P(x, \cdot) \ll \pi$, then $P$ is Harris recurrent. This condition is often used for Gibbs samplers. 

#### Irreducible Metropolis is Harris
Let $P$ be a $\pi$-irreducible Metropolis kernel. Then $P$ is Harris recurrent. 

#### Ergodicity
A Markov Chain is called ergodic if it is positive Harris recurrent and aperiodic. 

There are three additional stronger forms of ergodicity:

- **Ergodicity of degree $2$**: Let $S_B$ denote the hitting time for set $B$. An ergodic chain with invariant distribution $\pi$ is ergodic of degree $2$ if
$$
\int_B \mathbb{E}_x[S_B^2]  \\, \pi(dx) \\, < \\, \infty
$$
For these types of chains we have
$$
n\parallel P^n(x, \cdot) - \pi\parallel\_{\text{TV}} \to 0
$$
- **Geometric Ergodicity**: An ergodic Markov CHain with invariant distribution $\pi$ is geometrically ergodic if there exsts a non-negative extended real-valued function $M$ with $\pi(|M|) < \infty$ and a positive constant $r<1$ such that
$$
\parallel P^n(x, \cdot) - \pi\parallel\_{\text{TV}} \\,\leq \\, M(x) r^n \qquad\qquad \forall \\,\\, x
$$
Basically this means that TV norm is upper-bounded by a dampened function.
- **Uniform Ergodicity**: In addition, the chain is uniformly ergodic if there exists a positive constant $M$ and a positive constant $r < 1$ such that
$$
\text{sup}\_{x\in E} \parallel P^n(x, \cdot) - \pi\parallel \\, \leq \\, M r^n 
$$
Basically this means the sup of the TV norm is upper-bounded by a dampened constant. 

The three forms of ergodicity are related by the following relation
$$
\text{Uniform Ergodicity} \implies \text{Geometric Ergodicity} \implies \text{Degree 2 Ergodicity}
$$

#### Minorization and Small Sets
Suppose $P$ is a $\pi$-irreducible kernel. Let $m\geq 1$ be an integer, $\beta > 0$ be a constant, $C\in\mathcal{E}$ be a set and $\nu$ be a probability measure on $\mathcal{E}$. We say that the kernel $P$ satisfied a *minorization condition* $M(m, \beta, C, \nu)$ if $\pi(\text{$C$}) > 0$
$$
\beta \nu(\cdot) \leq P^m(x, \cdot) \qquad \qquad \forall \\, x\in C
$$
We say $C$ is a *small set* for $P$.

Basically it means this: Suppose we have a set $C$ with positive $\pi$ mass. If we start from any point $x\in C$ the $m$-step transition kernel $P^m(x, \cdot)$ is a measure that dominates $\beta \nu(\cdot)$.

#### Convergence results
- $P$ is uniformly ergodic $\iff$ the state space $E$ is small (i.e. a minorization condition $M(m, \beta, E, \nu)$ is satisfied)
- If $P$ satisfies a minorizatio condition $M(m, \beta, E, \nu)$ then the convergence rate $r$ satisfies
$$
r^m\\, \leq \\,(1 - \beta)
$$
- Suppose $\pi$ is a target measure and $q$ proposal measure, both of them bounded and bounded away from $0$ on $E^+$. Suppose the Lebesgue measure assigns finite measure to $E^+$, i.e. $\mu(E^+)<\infty$. The Metropolis Kernel targeting $\pi$ with proposal $q$ satisfies a minorization condition $M(1, \beta, E \nu)$ where $\nu \propto \mu\mid_{E^+}$. This means the **Metropolis kernel** is therefore **uniformly ergodic** because $E$ is a small set.
- An **Independence Metropolis** kernel with proposal density $f$ and bounded weight function $w = \pi/f$ satisfies a minorization condition $M(1, \beta, E, \pi)$ with $\beta = (\sup w)^{-1}$ and is thus **uniformly ergodic**. The convergence rate $r$ satisfies 
$$
r \\, \leq \\, (1 - \beta) = (1 - (\sup w)^{-1})
$$
- Uniform ergodicity for **mixtures**: Suppose $P_1$ and $P_2$ are $\pi$-invariant and $P_1$ is uniformly ergodic. The mixture kernel $\alpha P_1 + (1 - \alpha) P_2$ is uniformly ergodic.
- Uniform ergodicity for **cycles**: Suppose $P_1$ and $P_2$ are $\pi$-invariant and that $P_1$ satisfies a minorization condition $M(1, \beta, E, \nu)$ for some $\beta$ and $\nu$. Then $P_1 P_2$ and $P_2 P_1$ are uniformly ergodic.

#### Making mixtures/cycles ergodic
Since the independence kernel with bounded weigh function satisfies a minorization condition, then we can add this to any cycle or mixture of kernels to make the overall transition kernel uniformly ergodic. This basically means that we insert a "restart step". We need to make sure that $f$ has sufficiently thick tails.

#### Convergence of Averages
Let $X_n$ be an ergodic Markov Chain with equilibrium distribution $\pi$. Suppose the function $f$ is real-valued and $\pi(|f|) < \infty$. Then for any initial distribution we have
$$
\overline{f}\_n := \frac{1}{n}\sum\_{i=1}^n f(X_i) \quad \longrightarrow \quad \pi(f) \qquad \qquad \text{ almost surely}
$$

#### Central Limit Theorem (version 1)
Suppose $X_n$ is ergodic of degree $2$ with equilibrium distribution $\pi$. Suppose $f$ is real-valued and bounded. Then, there exists a real number $\sigma(f)$ such that the distribution of
$$
\sqrt{n}(\overline{f}\_n - \pi(f))
$$
converges weakly to a normal distribution wiht mean $0$ and variance $\sigma(f)^2$ for any initial distribution. 

#### Central Limit Theorem (version 2)
Suppose $X_n$ is uniformly ergodic with equilibrium distribution $\pi$ and suppose $f$ is real-valued and $\pi(f^2)<\infty$. Then here exists a real number $\sigma(f)$ such that the distribution of 
$$
\sqrt{n}(\overline{f}\_n - \pi(f))
$$
converges weakly to a normal distribution wiht mean $0$ and variance $\sigma(f)^2$ for any initial distribution. 


