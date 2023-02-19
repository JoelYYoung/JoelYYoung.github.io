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

**Def**: $\nabla'([l,h],f([l,h]))$ is defined as follows: 
$$
\nabla'([l,h],[l',h'])=\begin{cases}
l& l\le l'\\\\
max\\{x|x \in B \wedge x<l'\\}& l>l'
\end{cases}
$$
Note that since $f$ is already monotonic, so we actually always have $l' \le l$ so the first case happens when and only when $l=l'$ saying that **the iterated result will either remain unchanged or go upstairs along the newly defined $B$ lattice**.And $\nabla$ can be defined by combining the point wise $\nabla'$ like $\omega$, obmitted here.



**Problem**: Does this strategy make the whole iteration divergent? 

**Answer**: Hopefully, it won't! Note that only **unstables** variables causes divergency. 

**Proof**: We give an intuitive proof of this conclusion: it can be proved that the $\nabla$ function is monotonic, so the iteration will have its variables' states remain unchanged or increase along a limited **stairs** (a vivid metaphor).



#### Narrowing

**Brief**: Iteratively calculate $f(lfp(\omega \circ f))$ for arbitary times, until we get a satisfying result.

**Problem**: Why narrowing works?

Firstly, it can be proved from the defnition that the $lfp$ of $\omega \circ f$ and $\nabla\circ f$ are both **greater than** the $lfp$ of $f$. Secondly, we have $\forall \sigma, \sigma\sqsubseteq \omega(\sigma)$, i.e., $\omega$ is extensive. So $lfp_\omega=\omega(f(lfp_\omega))\sqsubseteq f(lfp_\omega)$.

**Note**: be careful to distinguish monotone and extensive, $f$ is monotonic but not extensive!

### Co-work with WTO to deal with cycles

#### WTO fixed-point algorithm

In previous iterative fixed-point algorithm, we recalculate the $OUT$ of every nodes in CFG during each iteration. And worklist algorithm improves performance by keeping a record to changed $OUT$ and only recalculate those changed nodes. However, when we look deeply in to the changes happing to the $OUT$ of each node, we will find that some moves are actually **unnecessary**. Take the following program as example:

```C
int x;
for(int i = 0; i < input; i+=x){
    for(int j = 0; j < input; j++){
        x += j*j;  // program point 1
    }
}
```

The iteration process of WTO fixed-point algorithm is as follows:

<center>
<img width="400" src="https://github.com/JoelYYoung/JoelYYoung.github.io/raw/master/static/img/WTO_example1.svg">
<div style="color:black;"> <b> WTO fixed-point algorithm: example 1 </b>  </div>
</center>


If we apply worklist fixed-point algorithm on it, then we would have to interpret `i=i+x` when `x` has **not been stable** yet, which causes unnecessary coasts. And WTO fixed-point algorithm interpret `i=i+x` only after `x` has iterated to a stable state through inner loop. Therefore, WTO algorithm has a better performance on this testcase.

**Question**: Is WTO fixed-point algorithm always faster than a normal *worklist algorithm*?

**Answer**: NOT necessary, take the following program as an example:

```C
for(int i = 0; i < input; i++){
    for(int j = i; j < input; j++){
        x = j * j; // program point 1
    }
}
```

The iteration process of WTO fixed-point algorithm is as follows:

<center>
<img width="800" src="https://github.com/JoelYYoung/JoelYYoung.github.io/raw/master/static/img/WTO_example2.svg">
<div style="color:black;"> <b> WTO fixed-point algorithm: example 2 </b>  </div>
</center>


Obviously, we iterate until widening on the inner loop during **each** iteration on the external loop (left picture), which can be avoided by doing interpreting all nodes at the same time (right picture).



#### Widen at *feedback vertex set* (FVS)

Based on the observation that **unstablity is resulted from loops**, we can apply widening only on one node of each loops, then we can asure the whole iteration process to stop after finite steps.

**Proof**: Does it promise to make the iteration finite?

Here we define a smaller lattice that is producted by the state lattices of the nodes in FVS, and we focus on the changes of the smaller lattice. After each iteration, the states of FVS will either increase or remain unchanged. And the states of other nodes will change for finite iteration rounds until the states of FVS change or remains unchanged and reach the fixed point. Because if states of FVS remains unchanged, it is equivalent to delete all feedback edges at each iteration rounds. So  

### Widen delay

We can apply to loops $f$ for the first several times, and then apply $\omega \circ f$ to get a more precise result.



## Summary

| fixed-point algo | widen on                | fixed-points                                   |
| ---------------- | ----------------------- | ---------------------------------------------- |
| WTO              | head, changed variables | $lfp(\nabla \circ f)$, $\nabla$ only for heads |
| worklist         | all variables           | $lfp(\omega)$                                  |

- WTO fixed-point doesn't influence precision, but just influences the speed!
- widen on head/changed variables actually incluences the precision!



## Appendex: Proof of WTO Fixed-point Algorithm

**def** $f_p(\sigma) = [\sigma_1, \sigma_2,...,\sigma_n']$

**def** $\nabla \circ f_p$ applying selective widening on $\sigma$s of FVS.

we have $\bot \sqsubseteq \nabla \circ f(\bot)$

**lemma 1**: $x\sqsubseteq f(x) \Rightarrow x\sqsubseteq f_p(x) \sqsubseteq f(x)$

**lemma 2**: $\nabla \circ f_p$ is monotonic.

**Conclusion **1: $\bot \sqsubseteq \nabla \circ f_p(\bot) \sqsubseteq\nabla\circ f(\bot)$, from lemma 1.

So $\nabla \circ f_p(\bot) \sqsubseteq \nabla \circ f(\nabla \circ f_p(\bot))$, from $\nabla\circ f(\bot)\sqsubseteq \nabla \circ f(\nabla \circ f_p(\bot))$

which means $\nabla \circ f_p(\bot) \sqsubseteq \nabla \circ f_p(\nabla \circ f_p(\bot)) \sqsubseteq\nabla\circ f(\nabla \circ f_p(\bot))$, from lemma 1.



then we replace $\bot$ with any $x$ that makes conclusion 1 true, and we will have if those conclusions also true for $\nabla\circ f_p(x)$.

finally, we will have a increasing sequence that satisfies:

- always increasing
- always $\sqsubseteq (\nabla \circ f)^n(\bot) \sqsubseteq lfp(\nabla \circ f)$

And we have proven that widening the sequence is finite, so it will finally reach the $lfp(\nabla \circ f)$.

## Reference

- [Static Program Analysis Chapter 6](https://cs.au.dk/~amoeller/spa/)
- [SPA Slide 5](https://cs.au.dk/~amoeller/spa/5-widening-and-narrowing.pdf)

