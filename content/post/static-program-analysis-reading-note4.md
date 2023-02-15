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
[l,u]\sqsubseteq[a,b] \Longleftrightarrow a<l \and b > u
$$

### Transfer Function and Control Flow Handling





## A New Framework: Lattice with Infinit Height



## Reference

- [Static Program Analysis Chapter 6](https://cs.au.dk/~amoeller/spa/)
- [SPA Slide 5](https://cs.au.dk/~amoeller/spa/5-widening-and-narrowing.pdf)

