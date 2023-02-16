+++
date = "2023-02-15"
title = "Static Program Analysis Reading Note: Chapter 6"
slug = "static-program-analysis-reading-note-chapter6"
categories = [ "Reading Note" ]
headline = "Widening and Narrowing"
tags = ["Static Program Analysis", "Reading Note"]
katex = true

+++

# Static Program Analysis Reading Note: Chapter 6

## Why Need *Widening*?

**Recap**: In Chapter 5, we introduced *Iterative Algorithm* and *Worklist Algorithm* to calculate the $lfp$ of the given constraint (for *May Analysis*). The correctness of the mentioned algorithm demands that the lattice has **limited height**.

**Problem**: What happens if the lattice has infinit height? E.g., in interval analysis, we use $\{[l,u]|l,u \in N \}$ as the lattice, which has infinit height.

**Intuitional Answer**: Take a simple loop as example,

```C
int i = input;
int x = 0;
for(int j = 0; j < i; j++){
    x = j * j;  // statement X
}
```

If we do analysis on this program, it will be following iterations:

1. $[i\mapsto[-\infin,+\infin],j\mapsto[0, 0],x\mapsto[0,0]]$
2. $[i\mapsto[-\infin,+\infin],j\mapsto[0, 1],x\mapsto[0,1]]$
3. $[i\mapsto[-\infin,+\infin],j\mapsto[0, 2],x\mapsto[0,4]]$
4. etc.

It will never stop ever!

## Interval Analysis

Before heading into widening, we give a formal definition of *Interval Analysis* first.

### Data Abstraction: Interval Lattice

In *Interval Analysis*, we use
$$
\{[l,u]|l,u \in N \}
$$
to represent a lattice, where
$$
N={-\infin,...,-2,-1,0,1,2,\infin}
$$
And We define the ralation on the lattice as follows
$$
[l,u]\sqsubseteq[a,b] \Longleftrightarrow a<l \wedge b>u
$$

### Transfer Function and Control Flow Handling

For any single statement $s_i$ that contains assignment operation, we define the transfer funtion as follows:
$$
f_s(IN[s_i])=eval(IN[s_i],E)
$$
,where $E$ is the right value of statement $x=E$. For statements that contains NO assignment operation, we have $f_s(IN[s_i])=IN[s_i]$. $eval(IN[s_i], E)$ is defined as follows according to what expression $E$ is:

1. if $E$ is a variable $y$, then we have $eval(IN[s_i],E)=IN[s_i][x\mapsto IN[s_i](y)]$
2. if $E$ is a constant value $C$, $eval(IN[s_i],E)=IN[s_i][x\mapsto [C,C]]$
3. if $E$ is an external input value, then $eval(IN[s_i],E)=IN[s_i][x\mapsto [-\infin,\infin]]$
4. else if $E$ is an expression $E_1 op E_2$, then we have: 

$$
eval(IN[s_i,E])=IN[s_i][x\mapsto [l,h]],
$$

$$
l=min\{a OPb|a\in eval(IN[S_i],E_1),b\in eval(IN[S_i],E_1)\},
$$

$$
h=max\{a OPb|a\in eval(IN[S_i],E_1),b\in eval(IN[S_i],E_1)\}.
$$

Control handling is simple that we take *least upper bound* of each $OUT$ of predecessors.

## A New Framework: Lattice with Infinit Height

### Does $lfp$ exist?

yes, it is $\sqcap\{x|x\in L, f(x)\sqsubseteq x\}$.

### Does *iterative algorithm* end?

not necessarily.

### Widening: step over infinit heights at one time

#### Narrowing

We iteratively calculate $f(lfp(\omega \circ f))$ for arbitary times, until we get a satisfying result.

#### Do not widen ALL nodes

We define $\nabla (\sigma_1, \sigma_2)$ to respect the former abstract states, and just apply $\omega'$ funtion (widen point wise) to those unstable, i.e., changed variables. 

### Co-work with WTO to deal with cycles

Furthermore, widen can be applied to only cycle head nodes (in WTO) in order to have a better solution.

### Widen delay

We can apply to loops $f$ for the first several times, and then apply $\omega \circ f$ to get a more precise result.



## Reference

- [Static Program Analysis Chapter 6](https://cs.au.dk/~amoeller/spa/)
- [SPA Slide 5](https://cs.au.dk/~amoeller/spa/5-widening-and-narrowing.pdf)

