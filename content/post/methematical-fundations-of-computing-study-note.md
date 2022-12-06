+++
date = "2022-12-05"
title = "Methematical Fundations of Computing: Study Note of CS103"
slug = "methematical-fundations-of-computing-study-note"
categories = [ "Methematical Fundations of Computing" ]
tags = ["Methematical", "Computing", "CS103"]
katex = true

+++

# Abstract

**Keywords**: Computing Model, Finite Automata (FA or DFA), Undeterministic Finite Automata (UFA), Problem, Decision Problem, Function Problem, Turing Machine (TM), Language, Recognizable Language, Regular Language, Decidable Language

Above are the major topic that will be talked in this artical. And this artical is basically a digest of [Stanford CS103](https://web.stanford.edu/class/archive/cs/cs103/cs103.1134/) course slides, which ignores the proof of some conclusion and detailed explainations of some ideas, but just lists the critical conclusions and notions that are helpful for understanding Static Program Analysis.



Advanced classes are [CS154](http://infolab.stanford.edu/~ullman/ialc/spr10/spr10.html) and [CS254](http://theory.stanford.edu/~liyang/teaching/complexity22.html). And this is [2018 version of CS103](http://web.stanford.edu/class/archive/cs/cs103/cs103.1184/), this is [2022 Version of CS103](https://web.stanford.edu/class/cs103/).

# Computability

## How to define the terminology *problem*?

> We want a definition that 
>
> - corresponds to the problems we want to solve, 
> - captures a large class of problems, and 
> - is mathematically simple to reason about. 
>
> No one definition has all three properties.

So we focus on ***Decision Problems***. 

> **Definition**: A ***decision problem*** is the problem of determining an answer to **a class of** yes/no **questions** about some **objects** of interest.



**Note**: the above definition points out that a ***decision problem*** should contain **a class of** yes/no questions，And these questions should be about some **objects** of interest. **"A class of"** here should be payed special attention to, which emphasizes that those questions should be of similar forms, i.e., they are about the **same** property of a **same** class of **objects**. **A class of objects** also have some restrictions, there must exist an alphabet $\Sigma$ that and the each object in the **class of objects** has an one-one correspondence to a specific **string** in the string set $\Sigma*$.

The other types of *problems* are *function problems*, which require to **take in an input and produce some output object rather than just a yes/no answer**.

***Decision Problems*** has the following advantages over none-decision problems:

- Convenient to reason methematically,
- Have simple methematical forms,
- etc.

## How do we *encode* a *problem*: *language*

> Any ***Decision Problems*** can be transformed into the problem of *recognizing strings' membership in a language*, i.e., to decide whether a given string ***belongs to*** a defined ***language***.

This claim seems unreasonable, but remember how we defined the terminology ***class of objects***? We have required  them to be *encodable*!

Since ***Solving Decision Problem = Strings Recognizing***, we can talk about ***Languages*** instead of a specific Decision Problem in the rest part of this artical. As for **how** a problem could be transformed, it's another story, and [this pdf](https://www.ccs.neu.edu/home/rjw/csu390-sp06/LectureMaterials/Decision-Problems.pdf) explains some details. Basically it is the notion same as ***encoding*** a class of objects or combinations of classes of objects.

Note that ***Finite Automata*** as a ***class of objects*** is encodable!

# Finite Automata

**Note**: this is correspondent to CS103 Lecture 11-13

> *automaton* (/ɔːˈtɒmətən/; plural: *automata* or *automatons*)

Three crutial aspects of finite automata:

- finite states
- transition over specific inputs (be with an ***alphabet*** $\Sigma$)
- only one ***start state*** & multiple ***stop states***

And we can define our automata's stop states by deviding them into 2 types: **accept states** and **reject states**, so that we can use this automata to solve ***Decision Problems***. 

## Language of FA: *Regular Languages*

**Definition**: Languages defined by *regular expression* are ***Regular Languages***. 

> **Conclusion**: For any single ***regular language***, there exists an finite automata that:
>
> - gets into ***Accept States*** and ***halts*** with string s, when s ***belongs to*** the language, and
> - gets into ***Reject States*** and halts with string s, when s does NOT ***belongs to*** the language.



We call a language that satisfies the above condition to a specific automata as *the language of that automata*.

## Undeterministic Finite Automata

to be completed...



# Turing Machines

A ***Turing Machine*** consists of:

- a finite automata as controler,
- a **infinite long** tape with a head pointing to one position on the tape

The ***Start State*** of a *Turing Machine* means:

- the FA is at the ***Start State*** and 
- the input string is somewhere on the tape (where exactly doesn't really matter), and if not given a input, the tape is defaultly set to all *Blank Character*.
- the head of the tape points to the first character of the input string.

The ***execution*** of a *Turing Machine* works by repeating the 3 steps until it gets into a ***Stop State***:

- Read the pointed character of the head as input
- Write back to the pointed position
- Transist to the next state

## Language of TM: *Recognizability* and *RE* Languages

We can define *Language of a Turing Machine* just like the *Language of a Finite Automata*, i.e., the set of strings containing all strings that could be the input of that TM and make the TM to get into an ***Accept State***.

Then we can define ***Recognizable Languages***, which is a sub set of ***Languages*** in which ***Language*** $L$ satisfies: there exisit a ***Turing Machine*** $M$ that $L$ is the language of $M$. We denote ***Recognizable Languages*** as $RE$ ***Languages***.

Note: **NOT** all languages has it's correspondent Turing Machine, only *RE* has!

## *Decidability* and *R​* Languages

Since the ***execution*** of a ***Turing Machine*** with an input s could be devided into exactly 3 categories: ending at ***Accept State***, ending at ***Reject State*** and does **NOT** end at all (i.e. diverge). And if we input a ***string*** $S \notin L$, then the ***Turing Machine*** $M$  may and may not stop.

If for any $S \notin L$, the ***Turing Machine*** $M$ stops (at an ***Reject State***), then $L$ is ***Decidable***, and the ***Turing Machine*** $M$ is a ***Decider***. We denote ***Decidable Languages*** as $R$ ***Languages***.

It is instantly available that $R \subseteq RE$, but is $R = RE$?

## Conclusion: *R ≠ RE*​!

It has been proved.

## How to *encode* a TM + its input?

We denote the encoding of ***Turing Machine*** $M$ plus its ***input string*** $s$ as $e<M,s>$ .

to be continued...



# Universal Turing Machines

**Definition**: a ***Universal Turing Machine*** ($U_{TM}$) takes in the encoding $e<M,s>$, where $M$ is any Turing machine and $s$ is any string in the set of $\Sigma*$. And the $U_{TM}$ should satisfy:

- it rejects $e<M,s>$ iff $M$ reject $s$,
- it accepts $e<M,s>$ iff $M$ accept $s$

> Theorem (Turing, 1936): There is a Turing machine UT called the universal Turing machine that, when run on an input of the form ⟨M, w⟩, where M is a Turing machine and w is a string, simulates M running on w and does whatever M does on w (accepts, rejects, or loops). [CS103 2018 Small22.pdf](http://web.stanford.edu/class/archive/cs/cs103/cs103.1184/lectures/22/Small22.pdf)



# Static Program Analyzer

## *Fail state* of a TM

> If we model programs as deterministic Turing machines, program failure can be modeled using a special fail state.2 That is, on a given input, a Turing machine will eventually halt in its accept state (intuitively returning “yes”), in its reject state (intuitively returning “no”), in its fail state (meaning that the correctness condition has been violated), or the machine diverges (i.e., never halts). A Turing machine is correct if its fail state is unreachable. [Static Program Anaysis Section 1.1](https://cs.au.dk/~amoeller/spa/spa.pdf)

## Does perfect universal analyzer exist?

> It is impossible to build a static program analysis that can decide whether a given program may fail when executed. Moreover, this result holds even if the analysis is only required to work for programs that halt on all inputs.

**Note**: such an analyzer is not a $U_{TM}$, because it takes in only $e<M>$ and accept not based on $M$'s execution on a single $s$, but based on any of $M$'s execution on all $s\in \Sigma*$. And it accepts when $M$'s fail state is ***unreachable***.

**Thinking**: is it possible to built such machine if we make more restriction on the input TM? 

# Reference

[Google Search Results](https://www.google.com/search?q=decision+problem+encode&oq=&aqs=chrome.2.69i59i450l8.405798079j0j15&sourceid=chrome&ie=UTF-8)

