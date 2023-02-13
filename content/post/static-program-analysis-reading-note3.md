+++
date = "2023-01-02"
title = "Static Program Analysis Reading Note: Chapter 5"
slug = "static-program-analysis-reading-note-chapter5"
categories = [ "Reading Note" ]
headline = "Dataflow Analysis with Monotone Frameworks"
tags = ["Static Program Analysis", "Reading Note"]
katex = true
modified = "2023-02-12"

+++

# Static Program Analysis Reading Note: Chapter 5

## Brief

***Recap*** How do we build a constraint system for a given program (CFG)?

We firstly define a *states* variable $[v]$ for each CFG vertex $v$, and we then generate constraints between those *states* variables, usually between neighboring vertexes, according to dataflow relations represented by the CFG and the programming language. Note that those constraints could be *equations* or *inequations*, and the latter could be transformed into *equations*. 

Then we can define a *constraint function* for each *equation* and solve the equation system by calculating *lfp* of the *combined constraint function*, if and only if the *combined constraint function* is monotone.

In order to solve the constraints with *fixed-points algorithm*, the key lies in making the *constraint functions* ***monotone***. And that's the topic today, the *monotone frameworks*. Note that *monotone constraints* may **NOT** generate the right answer, as shown in the pic in last article.

**Def**: *monotone framework* is the combination of a complete lattice and a space of monotone functions.

We classify those *monotone frameworks* according to the lattice system they use:

- Sign Lattice: Sign Analysis
- Constant Lattice: Constant Propagation Analysis
- etc

## Fix-Point Algorithm: a General Analysis Template

The monotonic constraint in Chapter 4 has an equivalent algorithm form. Its pseudo code is as follows:

```Python
OUT[EntryBlock] = some lattice value
for block in OtherBlocks:
    OUT[OtherBlocks] = some lattice value

while true:
    for block in AllBlocks:
        IN[block] = merge(OUT[predecessor_1], ..., OUT[predecessor_n])
        OUT[block] = transfer(IN[block])
	if no OUT changes:
        break
```

### worklist algorithm

Since a node need to be calculated if and only if its predecessors' $OUT$ have changed, so we could use a `worklist` to record the changed nodes and only apply calculation on them in each iteration. It saves a lot of time.

### WTO and "iterating until not change" strategy



## Constant Propagation Analysis



## Reach Definitions Analysis



## Reference

- [Static Program Analysis Chapter 5](https://cs.au.dk/~amoeller/spa/)
- [SPA Slide 4](https://cs.au.dk/~amoeller/spa/4-flow-sensitive-analyses.pdf)

