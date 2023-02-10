+++
date = "2022-12-27"
title = "Static Program Analysis Reading Note: Chapter 4"
slug = "static-program-analysis-reading-note-chapter4"
categories = [ "Reading Note" ]
headline = "Lattices and Fixpoints"
tags = ["Static Program Analysis", "Reading Note"]
katex = true
modified = "2023-02-10"

+++

# Static Program Analysis Reading Note: Chapter 4

## Basic Math Languages

**Def *partial order set (poset)***: A pair $(S,R)$. $S$ is a set; $R$ (also denoted as $\sqsubseteq$) is a *binary relation* on $S$ that satisfies the following rules:

- reflexivity: $\forall x \in S, x \sqsubseteq x$.
- transitivity: $\forall x,y,z \in S, x \sqsubseteq y \wedge y \sqsubseteq z \rightarrow x \sqsubseteq z$.
- anti-symmetry: $ \forall x, y \in S, x \sqsubseteq y \wedge y \sqsubseteq x \rightarrow x=y $

**Note**: If we use the terminology *partial order relation*, we usually denote $R$ instead of $(S,R)$.



**Def *upper bound/lower bound***: For $X\subseteq S$, the *upper bound* of $X$ is any $y \in S$ that satisfies $\forall x \in X, x \sqsubseteq y$, written $X \sqsubseteq y$. And *lower bound* is similarly defined. We use $\sqcup X$ to denote *least upper bound* (also called *lub*) of $X$, indicating $\forall X \sqsubseteq y, lub \sqsubseteq y$.



**Def *lattice***:  If a *partial order* $ (S,R)$ satisfies, $\forall x, y \in S, \exists \sqcup\{x,y\} \in S$, then it is a *lattice*.



**Def *complete lattice***: If a *partial order* $ (S,R)$ satisfies, $\forall X \subseteq S, \exists \sqcup X \in S$, then it is a *complete lattice*.



**Def *map lattice***: If $A$ is any set, and $L$ is a complete lattice, then we can define a set of functions $M$ that map from $A$ to $L$. And we can define a *partial relation* on $M$: $\forall f,g \in M$, $f \sqsubseteq g$ if and only if $\forall x \in S, f(x) \sqsubseteq g(x)$.



**Def *product lattice***: We can define the product *lattices* $L$, which is basically the product of a set $P=L_1 \times L_2$. And we can define a relation on the product $P$: $\forall (x1, x2), (x1',x2') \in P$,  $(x1, x2) \sqsubseteq (x1', x2')$ if and only if $x1 \sqsubseteq x1' \wedge x2 \sqsubseteq x2'$.



**Def *homomorphism***: If for 2 lattices $L_1$ and $L_2$, if function $f:L_1 \rightarrow L_2$ satisfies: $\forall x_1, x_2 \in L_1, x_1 \sqsubseteq x_2 \rightarrow f(x_1) \sqsubseteq f(x_2)$, then we say the function $f$ is *homomorphism*. A *bijective* homomorphism is call *isomorphism*.



**Def *isomorphic lattice pair***: If for 2 lattices $L_1$ and $L_2$, exists a isomorphism between them, than those 2 lattices are *isomorphic* to each other.



**Eg. Some $A$ makes product lattice $L^n$ and map lattice $A\rightarrow L$ *isomorphic***: This is an intuitive example of *isomorphic*, if $|A|=n$, and we define a function $f$ that maps between $L^n$ and $A\rightarrow L$ in the following way:

For some $g \in A\rightarrow L$ and $Ln \in L^n$, if $\forall i \in [1, n], g(a_i)=Ln_i$, then $f$ maps $g$ with the $Ln$, 

then the function $f$ is a *isomorphism*, and thus the product and map lattice are *isomorphic*. Note this property is essential to apply the theory of *lattice* to formalizing the process of *Sign Analysis*.

  

## How to Formalize *Sign Analysis* with the Above Defs?

We model a program into a *CFG*, that contains $n$ *CFG nodes*. And we define the ***states*** at a single program point into a *map lattice* $ML:Vars\rightarrow L$ where $Vars$ is a set of program's variables and $L$ is the ***sign lattice***. Then we can define the ***states*** of the whole program $ML^n$.

According to the whole program can build a set of constraints which can be formalized as $f(x) = x$, $x \in ML^n$, then the whole Sign Analysis job is formalized into solving this *equation*, and the solutions (the equation system could have multiple solutions) are the same things as the *fixed-points* of the function.

## Constraints: *Transfer functions* and *Control Flow Merges*

Given a simple CFG (no loops or branches), let $x_1, x_2, ..., x_n$ be the ***states*** of program points (CFG nodes), then we can generate constraints of node $i$ by the following 3 rules:

- if node $i$ is var declaring statement`var a`: $x_i = x_{i-1}[a = \top]$
- if node $i$ is var definition statement`a = c`: $x_i = x_{i-1}[a=x_{i-1}(c)]$
- all else statement: $x_i = x_{i-1}$

If the CFG contains loops or branches instead, we replace $x_{i-1}$ with the *least upper bound* of all predecessors' states of node $i$.

The above constraints are all ***equations***, and we can certainly define a ***constraint function*** for each *equation*: $f_i(x_1, x_2, ..., x_n): L^n \rightarrow L$, and combine them into one function $f(x_1, x_2, ..., x_n) = (f_1(...), f_2(), ..., f_n(...))$ . Then we can use $x_i = f_i(x_1, ..., x_n)$ to denote each equation and use $x = f(x)$ to denote all of the constraint equations.

> Theorem: $f$ is *monotone* if and only if $\forall i, f_i$ is *monotone*.

**Note**: The constraint function $f$ **is not always monotone**.

## Solutions to the constraints

Legal solutions to the constraint equations are the same things as the ***fixed-points*** of the *constraint function* $f$. So we can try to get the solution by calculating the *fix-point* of the *constraint function* $f$. 



> *fixed-point theorem*: In a complete lattice $L$ with finite height, every monotone function $f:L\rightarrow L$ has a unique least fixed point denoted $lfp(f)$ defined as: $lfp(f)=\mathop{\sqcup}\limits_{i\ge0}{f^i(\bot)}$



According to the *fixed-point theorem*, we can define an algorithm call *naive fixpoint solution*: for any $f$ is monotone, we can calculate $f(x)$ and use the output as input repeatedly and finally get to the fix point $x$ that makes $x = f(x)$.



**Note**: If $L$ does NOT have a finite height or $f$ is not monotone, then neither the theorem nor the algorithm work. So **our solution only works under the condition where $f$ is monotone and $L$ has finite height**, with is not always true for constraints generated by the 3 rules introduced in the last section.



**PS**: If the constraint system is not constructed into equations, it can still be transformed into equations! (*SPA* P49)

## Fixed points and Approximation answers

Fix points are approximation answer to the question "what abstract states are possible at each program point". And for ***May Analysis***, the  $lfp$ means the most *accurate* answer to the given constraint system. 

<center>
<img width="300" src="https://github.com/JoelYYoung/JoelYYoung.github.io/raw/master/static/img/fixed-point.svg">
<div style="color:black;"> <b> lattice points as answers </b>  </div>
</center>


**Note**: ***Fixed points*** are for each designed constraint, i.e., different constraints could yield different ***Fixed points*** and $lfp$; The above framework is path insensitive, and path sensitive approximation like *MOP*(meet over every path) is proved to be more precise for the same data abstraction. 

## *May Analysis* and *Must Analysis*

| May Analysis                                                 | Must Analysis                                                |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| - Give answers based on a collection that covers **ALL** possible ***run time situations*** in order to be ***safe***. | - Give answers based on a collection that exclude any **impossible** ***run time situations*** in order to be ***safe***. |
| - From $\bot$ to do iterations, gets least fixed-point       | - From $\top$ to do iterations, gets biggest fixed-point     |
| - Usually used for bug detection and software verification, e.g., **pointer analysis** | - Usually used for compilation optimization, e.g., **constant propagation** |

For the same data abstraction, they have the same ***Truth*** answer.

## Summary

The key lies in designing *monotonic*, *safe* guaranteed and *precise* transfer functions.

- [Static Program Analysis Chapter 4](https://cs.au.dk/~amoeller/spa/)
- [SPA Slide 3](https://cs.au.dk/~amoeller/spa/3-lattices-and-fixpoints.pdf)

