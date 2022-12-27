+++
date = "2022-12-27"
title = "Static Program Analysis Reading Note: Chapter 4"
slug = "static-program-analysis-reading-note-chapter4"
categories = [ "Reading Note" ]
headline = "Lattices and Fixpoints"
tags = ["Static Program Anaysis", "Reading Note"]

+++

# Static Program Analysis Reading Note: Chapter 4

## Basic Math Languages

**Def *partial order***: A *set* $S$ as well as a *binary relation* $R$ on $S$ that satisfies the following rules:

- reflexitivity: $\forall x \in S, x R x$.
- transitivity: $\forall x, y, z \in S, xRy \and yRz \rightarrow xRz$
- anti-semmetry: $\forall x, y \in S, x R y \and y R x \rightarrow x=y$

**Note**: If we denote a partial order, we usually refer to $S$ instead of $(S,R)$.



**Def *upper bound/lower bound***: For $X\subseteq S$, the *upper bound* of $X$ is any $y \in S$ that satisfies $\forall x \in X, x R y$. We use $X \sqsubseteq y$ to denote that. And *lower bound* is similarly defined. We use $\sqcup X$ to denote any single *upper bound* of $X$.

**Note**: $xRy$ is the same as $x\sqsubseteq y$.



**Def *lattice***:  If a *partial order* $ (S,R)$ satisfies, $\forall x, y \in S, \exists \sqcup\{x,y\} \in S$, then it is a *lattice*.



**Def *complete lattice***: If a *partial order* $ (S,R)$ satisfies, $\forall X \subseteq S, \exists \sqcup X \in S$, then it is a *complete lattice*.



**Def *map lattice***: If $A$ is any set, and $L$ is a complete lattice, then we can define a set of functions $M$ that map from $A$ to $L$. And we can define a *partial relation* on $M$: $\forall f,g \in M$, $f \sqsubseteq g$ if and only if $\forall x \in S, f(x) \sqsubseteq g(x)$.



**Def *product lattice***: We can define the product *lattices* $L$, which is basically the product of a set $P=L_1 \times L_2$. And we can define a relation on the product $P$: $\forall (x1, x2), (x1',x2') \in P$,  $(x1, x2) \sqsubseteq (x1', x2‘)$ if and only if $x1 \sqsubseteq x1' \and x2 \sqsubseteq x2'$.



## How to Formalize *Sign Analysis* with the Above Defs?

*to be completed.*



## Reference

