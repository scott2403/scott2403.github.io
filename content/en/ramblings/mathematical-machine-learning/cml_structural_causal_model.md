---
title: Structural Causal Model
linktitle: Structural Causal Model
toc: true
type: docs
date: "2019-05-05T00:00:00+01:00"
draft: false
menu:
  mathematical-machine-learning:
    parent: 3) Causal ML
    weight: 8

# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 8
---
Often Randomized Controlled Trials (RCT) are not feasible, typically for ethical reasons. A **Structural Causal Model** (SCM) allows one to answer questions about the observational data *without intervention*. Originally invented by Judea Pearl, which defines them as an ordered triplet $\langle U, V, E \rangle$ where
- $U$: set of **exogenous variables** (values are determined by factors outside the model)
- $V$: set of **endogenous variables** (values are determined by factors within the model)
- $E$: set of **structural equations** defining values in $U$ based on values in $U$ and $V$.

Judea Pearl talks about the ladder of causation:

1. **Association**: $A$ is associated to $B$ if observing $A$ changes the probability of observing $B$. Can be computed via *correlations*. These have no causal implication. We write $P(A \mid B)$.
2. **Intervention**: Assesses casuality by performing an experimental intervention. We write $P(A \mid \text{do} B)$.
3. **Counterfactuals**: Considering an alternate version of a past event, what would have happened under different circumstances to the same experimental unit? Can be used to indicate causal relationships.

Typically in SCMs we use **causal graphs** which displays causal relationships between variables in the model. If there is an arrow from node $A$ to node $B$ it mean that $A$ *causes* $B$. There are several types of graphs, including:

1. **Causal Loop Diagrams**
2. **Directed Acyclic Graphs**
3. **Ishikawa Diagrams**

Elements of a causal graph:

1. **Chain**: $A\longrightarrow B \longrightarrow C$, here $B$ is an mediator.
2. **Fork**: $A \longleftarrow B \longrightarrow C$: one cause has multiple effects. This creates a correlation between $A$ and $C$ which can be removed by conditioning on $B$.
3. **Collider**: $A\longrightarrow B \longleftarrow C$ multiple causes affect one outcome.

