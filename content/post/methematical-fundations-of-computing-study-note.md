+++
date = "2022-12-05"
title = "Methematical Fundations of Computing: Study Note of CS103"
slug = "methematical-fundations-of-computing-study-note"
categories = [ "Methematical Fundations of Computing" ]
tags = ["Methematical", "Computing", "CS103"]

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

So we focus on ***Decision Problems***, which are defined to be **problems with yes/no answers**.  The other types of *problems* are *function problems*, which require to **take in an input and produce some output object rather than just a yes/no answer**.

***Decision Problems*** has the following advantages over none-decision problems:

- Convenient to reason methematically,
- Have simple methematical forms,
- etc.

## How do we *encode* a *problem*: *language*

> Any ***Decision Problems*** can be transformed into the problem of *Recognizing a Language*, i.e., to decide whether a given string ***belongs to*** a defined ***language***.



Since ***Decision Problem = Language Recognizing***, we can talk about ***Languages*** instead of a specific Decision Problem in the rest part of this artical. As for **how** a problem could be transformed, it's another story.



# Finite Automata

**Note**: this is correspondent to CS103 Lecture 11-13

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

## Language of TM: *Recognizability* and $RE$ Languages

We can define *Language of a Turing Machine* just like the *Language of a Finite Automata*, i.e., the set of strings containing all strings that could be the input of that TM and make the TM to get into an ***Accept State***.

Then we can define ***Recognizable Languages***, which is a sub set of ***Languages*** in which ***Language*** $L$ satisfies: there exisit a ***Turing Machine*** $M$ that $L$ is the language of $M$. We denote ***Recognizable Languages*** as $RE$ ***Languages***.

## *Decidability* and $R$ Languages

Since the ***execution*** of a ***Turing Machine*** with an input s could be devided into exactly 3 categories: ending at ***Accept State***, ending at ***Reject State*** and does **NOT** end at all (i.e. diverge). And if we input a ***string*** $S \notin L$, then the ***Turing Machine*** $M$  may and may not stop.

If for any $S \notin L$, the ***Turing Machine*** $M$ stops (at an ***Reject State***), then $L$ is ***Decidable***, and the ***Turing Machine*** $M$ is a ***Decider***. We denote ***Decidable Languages*** as $R$ ***Languages***.

It is instantly available that $R \subseteq RE$, but is $R = RE$?

## Conclusion: $R \neq RE$!

It has been proved.

## How to *encode* a TM + its input?

We denote the encoding of ***Turing Machine*** $M$ plus its ***input string*** $S$ as $e<M,S>$ .



# Universal Turing Machines

**Definition**: a ***Universal Turing Machine*** takes in the encoding $e<M,S>$.



# Static Program Analyzer: Universal TM

## *Fail state* of a TM

