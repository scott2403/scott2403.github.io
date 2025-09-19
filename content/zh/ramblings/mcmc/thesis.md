---
title: Theory
linktitle: Theory
toc: true
type: docs
date: "2019-05-05T00:00:00+01:00"
draft: false
menu:
  mcmc:
    parent: 1) MCMC Literature
    weight: 2

# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 2
---
$\newcommand{\ystar}{y^{\*}}
\newcommand{\Ycal}{\mathcal{Y}}
\newcommand{\isample}{^{(i)}}
\newcommand{\setX}{\mathsf{X}}
\newcommand{\algebraX}{\mathcal{X}}
\newcommand{\Fcal}{\mathcal{F}}
\newcommand{\Xcal}{\mathcal{X}}
\newcommand{\Pcal}{\mathcal{P}}
\newcommand{\Ycal}{\mathcal{Y}}
\newcommand{\Rbb}{\mathbb{R}}
\newcommand{\Ebb}{\mathbb{E}}
\newcommand{\Pbb}{\mathbb{P}}
\newcommand{\Psf}{\mathsf{P}}
\newcommand{\Xsf}{\mathsf{X}}
\newcommand{\Ysf}{\mathsf{Y}}$
$$
\newenvironment{rcases}
  {\left.\begin{aligned}}
  {\end{aligned}\right\rbrace}
$$

# Measure-Theoretic Probability
## Probability Spaces
Intuitively, a probability is a quantitative measure of the likelihood of an event happening. But what is an event? We will consider events only sets that are well behaved so that we can assign probabilities to them in a coherent fashion. All events under consideration in a given set up can be grouped together into a set called a "sigma algebra".

### Sigma Algebra
Let $\Xsf$ be a non-empty set. We call $\Xcal\subseteq \Pcal(\Xsf)$ a sigma algebra on $\Xsf$ if  satisfies:

- $\Xsf\in\Xcal$.
- It is closed under complementation: $A\in\Xcal \quad \implies \quad A^c:= \Xsf \backslash A \in \Xcal$
- It is closed under countable unions: $A_1, A_2, \ldots \in \Xcal \quad \implies \quad \bigcup_i A_i \in \Xcal$

Any set $A\in\Xcal$ we say it is $\Xcal$-**measurable**. If $\Xsf$ is the set of all possible outcomes, we say that $\Xcal$ is the space of possible events or the set of measurable events. In other words, a sigma algebra contains sets to which we can assign a size or, more precisely in our case, a probability.

We can now proceed to defining how one might go about assigning a probability to a set. The most obvious way to do this is with a function with some additional structure.

### Measure
Let $\Xcal$ be a sigma-algebra over the set $\Xsf$. A function $\mu: \Xcal \to \Rbb\cup\\{\pm \infty\\}$ is called a measure if it satisfies:

- Non-negativity $\mu(E)\geq 0\qquad \qquad \forall\\, E\in \Xcal$ 
- Null empty set $\mu(\emptyset) = 0$
- Countable additivity: If $\\{E_i\\}$ is a countable collection of pairwise disjoint sets in $\Xcal$ then
$$
\mu\left(\bigcup_i E_i \right) = \sum_i \mu(E_i)
$$

Hence $\mu$ assigns a "size" to elements of a sigma algebra. In the particular case in which $\mu(\Xsf)=1$ we say that $\mu$ is a **probability measure**. Indeed if $\Xsf$ is the space of all outcomes and $\mu$ is defined on $(\Xsf, \Xcal)$ then $\mu(X)=1$ simply means that the probability of anything happening at all is $1$, which is in accordance with out usual understanding of probability. 

Notice that in the definition of measure we allow $\mu$ to take $\pm \infty$. This would cause troubles in the world of probability so we give a name to those measures that assign finite size to $\Xsf$. If $\mu(\Xsf) < \infty$ then we say that $\mu$ is a **sigma-finite measure**. Importantly, any sigma-finite measure $\mu$ is essentially equivalent (up to rescaling) to a probability measure since one can simply define 
$$
\mathring{\mu} = \frac{1}{\mu(X)}\mu
$$
which is obviously a probability measure.

The pair $(\Xsf, \Xcal)$ is called a **measurable space** while the triplet $(\Xsf, \Xcal, \mu)$ is called a **measure space**. When $\mu$ is a probability measure defined on $(\Xsf, \Xcal)$ then we say $(\Xsf, \Xcal, \mu)$ is a **probability space**.

We can write the size of a set $A\in\Xcal$ as an integral as follows
$$
\mu(A) = \int_A d \mu
$$

## Densities and Distributions 
Right now we can measure sets but what we need is to be able to measure sets according to our problem at hand, which will be different every time. This is where the concept of a random variable will come to the rescue. A random variable essentially is a well-behaved function that takes an outcome $x\in\Xsf$ and maps it to a value $y\in\Ysf$ that is coherent with our problem. For instance, if our outcomes are either head or tails $\Xsf = \\{\text{Head}, \text{Tail}\\}$ then the random variable might assign equal probabilities to each of them in fair coin $Z(\text{Head}) = 0.5$ and $Z(\text{Tail}) = 0.5$.

### Random Variable
Let $(\Xsf, \Xcal)$ and $(\Ysf, \Ycal)$ be two measurable spaces. A function $Z:\Xsf \to \Ysf$ is a random variable if for every $\Ycal$-measurable set $B\in\Ycal$, its pre-image $Z^{-1}(B)$ is $\Xcal$-measurable, that is $Z^{-1}(B)\in\Xcal$.
$$
Z^{-1}(B) \in \Xcal \qquad \qquad \forall \\, B\in\Ycal
$$
The pre-image is then defined as the set of all outcomes $x\in\Xsf$ that are mapped onto the set $B\in\Ycal$ by the random variable $Z$
$$
Z^{-1}(B) := \left\\{x\in\Xsf\\, :\\, Z(x) \in B\right\\}
$$
A random variable is also called a **measurable function**. 

### Distribution
Let $(\Xsf, \Xcal, \mu)$ be a probability space and $(\Ysf, \Ycal)$ a measurable space. Let $Z: \Xsf \to \Ysf$ be a random variable between $(\Xsf, \Xcal)$ and $(\Ysf, \Ycal)$. The probability distribution of $\Xsf$, denoted by $P_Z$, is the **pushforward measure** of $\mu$ by $Z$
$$
P_Z = \mu \circ Z^{-1} \qquad \qquad P_Z: \Ycal \to [0, 1]
$$
To specify which measure is being pushed-forward and by which measurable function one can write $P\_Z = Z\_*\mu$. The distribution $P_Z$ is a probability measure on $(\Ysf, \Ycal)$. 

It is important to notice how this distribution is constructed. Originally given a probability measure $\mu$ that could only assign probabilities to $A\in\Xcal$. We have then introduced a random variable to be able to map outcomes to values via $Z$ according to our application. Because of this, we would now like to give a probability to sets $B\in\Ycal$ since these are the events we are actually interested in. To give a measure to the set $B\in\Ycal$ we basically find its pre-image using the random variable (which by definition is $\Xcal$-measurable) and then we assign to $B$ the same probability that $Z^{-1}(B)$ has according to $\mu$
$$
P_Z(B) = \mu \circ Z^{-1}(B)
$$

We have seen that the distribution of a random variable is a measure. However, in practice we often don't work directly with distributions but with probability density functions. The best way to develop an intuition about the relationship between a distribution and a probability density function is through a phisical analogy. One could interpret the "base" probability measure $\mu$ to somehow give us an indication of volume, while we could interpret the probability measure $P_x$ as giving an indication of "mass". In this analogy, the probability density function is basically giving us the density of the random variable $Z$ with respect to $\mu$ and $P_Z$
$$
\text{density} = \frac{\text{mass}}{\text{volume}}
$$

### Density
Let $(\Xsf, \Xcal, \mu)$ be a probability space and $(\Ysf, \Ycal)$ be a measurable space. Let $Z:\Xsf \to \Ysf$ be a random variable between $(\Xsf, \Xcal)$ and $(\Ysf, \Ycal)$ with probability distribution $P_Z$ as the pushforward measure $P\_Z = Z\_*\mu: \Ycal \to [0, 1]$. Suppose also that $\lambda$ is another measure, called the reference measure, on $(\Ysf, \Ycal)$ and that both $P_Z$ and $\lambda$ are sigma-finite measures. Finally, suppose that the probability distribution is absolutely continuous with respect to the reference measure $P_Z \ll \lambda$. Then the probability density function $Z$ is defined as the Radon-Nikodym derivative of $P_Z$ with respect to $\lambda$
$$
p_z = \frac{d P_Z}{d \lambda}
$$
Then we can use the probability density function to write the measure of a set $A\in \Xcal$ according to $P_Z$ based on $\mu$
$$
P_Z(A) = \int_A d P_Z = \int_A \frac{d P_Z}{d \lambda} d\lambda = \int_A p_z d\lambda 
$$
Most often $\lambda$ will be the Lebesgue measure (here denoted $dz$) and therefore we can write the probability of event $A$ according to the distribution $P_Z$ in the more familiar form
$$
P_Z(A) = \int_A p_z(z) dz
$$

## Expectations
### Expected Value
Let $(\Xsf, \Xcal, \mu)$ be a probability space and $(\Ysf, \Ycal)$ be a measurable space. Let $Z:\Xsf\to\Ysf$ be a random variable. The expected value of $Z$ is 
$$
\Ebb[Z] = \int\_{\Xsf} Z d\mu = \int\_{\Xsf} Z(x) d \mu(x)
$$
Basically it is the average value of the measurable function $Z$ according to $\mu$, the measure defined on the "input" measurable space $(\Xsf, \Xcal)$.

The following is an extremely useful trick that is often used to manipulate integrals in measure-theory and will allow us to write the expected value in a much more familiar form.

### Change of Variables
Let $(\Xsf, \Xcal, \mu)$ be a probability space and $(\Ysf, \Ycal)$ be a measurable space. Let $Z:\Xsf\to \Ysf$ be a random variable with distribution $P\_Z = Z\_{\*}\mu$. Let $f: \Ysf \to [0, +\infty]$ be a measurable function. Then
$$
\int\_{\Ysf} f d P\_Z = \int\_{\Ysf} f d Z\_{\*} \mu = \int\_{Z^{-1}(\Ysf)} f \circ Z d \mu = \int_{\Xsf} f \circ Z d\mu 
$$
If you look at the second and at the last integral you can notice that the change of variables formula essentially amounts to a change of probability spaces between $(\Ysf, \Ycal, Z\_{\*}\mu)$ and $(\Xsf, \Xcal, \mu)$.

Notice also that the change of variables formula can be written as
$$
\Ebb\_{Z\_{\*}\mu}[f] = \Ebb\_{\mu}[f \circ Z]
$$
Indeed the change of variables formula allow us to write down the definition of expected value in the a more useful way. First of all, let $\text{Id}:\Ysf\to\Ysf$ be the identity function on $\Ysf$ mapping $\text{Id}(y) = y$ for every $y\in\Ysf$. Then the function $\text{Id}\circ : \Xsf \to \Ysf$ is measurable and so it is a random variable and indeed it identical to the random variable as $Z$. We can now write the expectation as follows
$$
\begin{align}
\Ebb[Z]
&= \Ebb[\text{Id} \circ Z] && \text{$\text{Id}\circ Z = Z$} \newline
&= \int\_{\Xsf} \text{Id} \circ Z d\mu && \text{Def of Expectation} \newline
&= \int\_{Z(\Xsf)} \text{Id} \\, dZ\_{\*} \mu && \text{Change of Variables} \newline
&= \int\_{\Ysf} \text{Id}(y) \\, dP_z(y) && \text{Def of $P_Z$} \newline
&= \int\_{\Ysf} y \frac{d P_Z}{d y} dy && \text{Def $\text{Id}$ and $P_Z \ll dy$} \newline
&= \int\_{\Ysf} y p_z(y) dy && \text{Radon-Nikodym}
\end{align}
$$

Where we have assumed that we have access to a measure $dy$ on $(\Ysf, \Ycal)$ such that $P_Z \ll dy$, i.e. $P_Z$ is absolutely continuous with respect to $dy$. Alternatively we can also say that $dy$ dominates or is a dominating measure for $P_Z$.

One can generalize the result above to any measurable function. This result is sometimes called Law of the Unconscious Statistician.

### Expectation of a Function








