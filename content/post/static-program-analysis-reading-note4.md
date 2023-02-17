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
4. $...$

It will never stop!

## Interval Analysis

Before heading into widening, we give a formal definition of *Interval Analysis* first.

### Data Abstraction: Interval Lattice

In *Interval Analysis*, we use
$$
\\{[l,u]|l,u \in N\\}
$$
to represent a lattice, where
$$
N=\\{-\infin,...,-2,-1,0,1,2,\infin\\}
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
4. else if $E$ is an expression $E_1 op E_2$, then we have $eval(IN[s_i,E])=IN[s_i][x\mapsto [l,h]]$, where

$$
l=min\\{a\\, op\\, b|a\in eval(IN[S_i],E_1),b\in eval(IN[S_i],E_1)\\},
$$


$$
h=max\\{a\\, op\\, b|a\in eval(IN[S_i],E_1),b\in eval(IN[S_i],E_1)\\};
$$



Control handling is simple that we take *least upper bound* of each $OUT$ of predecessors.

## A New Framework: Lattice with Infinit Height

### Does $lfp$ exist?

**Answer**: Yes! it is $d=\sqcap\{x|x\in L, f(x)\sqsubseteq x\}$.

**Proof**: We just need to prove $d$ is a fixed point, i.e., $f(d) = d$. 

Let $D=\\{x|x\in L\\}$, then we have $\forall x \in D, d\sqsubseteq x$. Therefore, $f(d)\sqsubseteq f(x) \sqsubseteq x$. So $f(d)$ **is also a lower bound of** $D$. Since $d$ is the **greatest** lower bound of $D$, we have $f(d)\sqsubseteq d$ instantly. Therefore, we have a valueable conclusion that $d \in D$. Then we further have $f(f(d))\sqsubseteq f(d)$, saying $f(d)\in D$. Since d is the greatest **lower bound** of $D$, we have our second conclusion that $d \sqsubseteq f(d)$.  The two conclusion combines to prove that $f(d)=d$.

### Does *iterative algorithm* end?

**Answer**: NOT necessarily! 

E.g., in the above example of a simple loop, the iteration will never come to an end. An intuitive explaination is that although the $lfp$ is proved to uniquely exist, it takes **infinite steps** to iterate from $\bot$ to $lfp$. 

### Widening: step over infinit heights at one time

#### An insight into widening

Since iterating from $\bot$ to $lfp$ **one step at a time** seems to take infinite rounds, we would like to solve the problem by stepping over those inifite steps at one time, and that's how widening works.

We can achieve this by mapping the infinit interval lattice to an finite one. This is done by restricting the interval lattice's $l$ and $h$ to a **finite set** $B$, which contians $-\infin,\infin$ and several other integers. Those integers can be specially choosen (heuristically), e.g., all integers occurring in the program.  

We just need to apply $\omega'$ function to each variable respectively after calculating $f_s(IN[s_i])$ of each node iteratively. The $\omega'$ function is defined as follows:$\omega'([l,h])=[a, b]$, where
$$
a=max\\{i\in B | i < l\\},\\\\ b=min\\{i\in B|i>h\\}.
$$
We can then define the constraint of all nodes as $w(f(\sigma))$, where $f$ is defined by control flow handling and transfering rules, and $\sigma =[\sigma_1, \sigma_2, ..., \sigma_n]$. $\omega$ function is defined as follows:
$$
\sigma''=\omega(\sigma')=w(\sigma_1', \sigma_2', ..., \sigma_n')=[\sigma_1'', \sigma_2'', ..., \sigma_n'']
$$
where
$$
\sigma_i''(X)=w'(\sigma_i'(X))
$$
note that $\sigma_i(X)$ means the abstract state of variable $X$ at program point $i$.

#### Do not widen ALL variables

We define $\nabla (\sigma, \sigma')$ to respect the former abstract states, and just apply $\omega'$ funtion (widen point wise) to those unstable, i.e., changed variables. $\nabla$ is mostly the same as $\omega$ except that it takes the former program state $\sigma$ additionally. It uses $\sigma$ to evaluate if variable $X$ is stable at some program point and only do widening to those unstable variables.

Here is an example of how this strategy helps to improve precision:

```C
for(int i = 0; i < input; i++){
    x = 8;
    y = i * i;
}
```

Assume $B=\\{-\infin,-1,0,1,7,\infin\\}$, if we do widening to $x$, then we will get $x\mapsto[7,\infin]$, but if we do not do widening on $x$, we will have $x\mapsto [8,8]$, which is obviously more precise than the former result.



Won't this strategy make the whole iteration divergent? Hopefully, it won't! Note that only **unstables** variables causes divergency. 

#### Narrowing

We iteratively calculate $f(lfp(\omega \circ f))$ for arbitary times, until we get a satisfying result.



### Co-work with WTO to deal with cycles

#### WTO fixed-point algorithm

An insight into the changes happens in each program points.

#### Widen at cycle head

Furthermore, widen can be applied to only cycle head nodes (in WTO) in order to have a better solution.

### Widen delay

We can apply to loops $f$ for the first several times, and then apply $\omega \circ f$ to get a more precise result.



## Reference

- [Static Program Analysis Chapter 6](https://cs.au.dk/~amoeller/spa/)
- [SPA Slide 5](https://cs.au.dk/~amoeller/spa/5-widening-and-narrowing.pdf)
