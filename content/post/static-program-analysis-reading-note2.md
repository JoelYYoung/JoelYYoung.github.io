+++
date = "2022-12-27"
title = "Static Program Analysis Reading Note: Chapter 4"
slug = "static-program-analysis-reading-note-chapter4"
categories = [ "Reading Note" ]
headline = "Lattices and Fixpoints"
tags = ["Static Program Anaysis", "Reading Note"]
katex = true

+++

# Static Program Analysis Reading Note: Chapter 4

## Basic Math Languages

**Def *partial order***: A *set* $S$ as well as a *binary relation* $R$ on $S$ that satisfies the following rules:

- reflexitivity: $\forall x \in S, x R x$.
- transitivity: $\forall x,y,z \in S, x R y \wedge y R z \rightarrow x R z$.
- anti-semmetry: $ \forall x, y \in S, x R y \wedge y R x \rightarrow x=y $

**Note**: If we denote a partial order, we usually refer to $S$ instead of $(S,R)$.



**Def *upper bound/lower bound***: For $X\subseteq S$, the *upper bound* of $X$ is any $y \in S$ that satisfies $\forall x \in X, x R y$. We use $X \sqsubseteq y$ to denote that. And *lower bound* is similarly defined. We use $\sqcup X$ to denote any single *upper bound* of $X$.

**Note**: $xRy$ is the same as $x\sqsubseteq y$.



**Def *lattice***:  If a *partial order* $ (S,R)$ satisfies, $\forall x, y \in S, \exists \sqcup\{x,y\} \in S$, then it is a *lattice*.



**Def *complete lattice***: If a *partial order* $ (S,R)$ satisfies, $\forall X \subseteq S, \exists \sqcup X \in S$, then it is a *complete lattice*.



**Def *map lattice***: If $A$ is any set, and $L$ is a complete lattice, then we can define a set of functions $M$ that map from $A$ to $L$. And we can define a *partial relation* on $M$: $\forall f,g \in M$, $f \sqsubseteq g$ if and only if $\forall x \in S, f(x) \sqsubseteq g(x)$.



**Def *product lattice***: We can define the product *lattices* $L$, which is basically the product of a set $P=L_1 \times L_2$. And we can define a relation on the product $P$: $\forall (x1, x2), (x1',x2') \in P$,  $(x1, x2) \sqsubseteq (x1', x2')$ if and only if $x1 \sqsubseteq x1' \wedge x2 \sqsubseteq x2'$.



**Def *homomorphisim***: If for 2 lattices $L_1$ and $L_2$, if function $f:L_1 \rightarrow L_2$ satisfies: $\forall x_1, x_2 \in L_1, x_1 \sqsubseteq x_2 \rightarrow f(x_1) \sqsubseteq f(x_2)$, then we say the function $f$ is *homomorphisim*. A *bijective* homomorphisim is call *isomorphisim*.



**Def *isomorphic lattice pair***: If for 2 lattices $L_1$ and $L_2$, exists a isomorphisim between them, than those 2 lattices are *isomorphic* to each other.



**Eg. Some $A$ makes product lattice $L^n$ and map lattice $A\rightarrow L$ *isomorphic***: This is an intuitive example of *isomorphic*, if $|A|=n$, and we define a function $f$ that maps between $L^n$ and $A\rightarrow L$ in the following way:

For some $g \in A\rightarrow L$ and $Ln \in L^n$, if $\forall i \in [1, n], g(a_i)=Ln_i$, then $f$ maps $g$ with the $Ln$, 

then the function $f$ is a *isomorphisim*, and thus the product and map lattice are *isomorphic*. Note this property is essential to apply the theory of *lattice* to formalizing the process of *Sing Analysis*.

  

## How to Formalize *Sign Analysis* with the Above Defs?

We model a program into a *CFG*, that contains $n$ *CFG nodes*. And we define the ***states*** at a single program point into a *map lattice* $ML:Vars\rightarrow L$ where $Vars$ is a set of program's variables and $L$ is the ***sign lattice***. Then we can define the ***states*** of the whole program $ML^n$.

According to the whole program can build a set of constraints which can be formalized as $f(x) = x$, $x \in ML^n$, then the whole Sign Analysis job is formalized into solving this *equation*, and the solution is called *fixed point* of the function.

## Reference

- [Static Program Analysis Chapter 4](https://cs.au.dk/~amoeller/spa/)
- [SPA Slide 3](https://cs.au.dk/~amoeller/spa/3-lattices-and-fixpoints.pdf)