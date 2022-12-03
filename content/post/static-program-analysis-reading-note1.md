+++
date = "2022-12-03"
title = "Static Program Analysis Reading Note: Introduction"
slug = "extending-this-logic"
categories = [ "Reading Note" ]
tags = ["Static Program Anaysis", "Reading Note"]

+++

# Target of SPA

***Target 1***: To answer any none-trivial questions of a program at given *program point*, e.g.

- **Whether** the judgement that `x > const` always true, i.e., invariant.
- **What** is the possible range of a number x?



***Target 2***: To answer any questions of a program, e.g.

- **How** much memory could the program consume?
- **Whether** the program terminates on all legal inputs?



They concerns both "yes-or-no" questions and more general questions. As to two typical problems, *bug detecting* and *program verification*, we can sovle them by reducing to a basic questions: "is each statement error free?".

# "Perfect" Analyzer

A perfect analyzer should be able to give the **correct** answer to all "yes-or-no" questions. Theoratically, correctness can be judged by considering the real execution.



# Approximation Analyzer

However, we are facing engeneering challenges like space and time to be **perfect**, i.e., to be always **correct**.



So for some **practical seneries**, we give **approximation answers**. And they are alway required to be ***conservative*** basically.  *Conservative* means "make the **safe** judgement", i.e., when we are **not sure enough** about a question, there are **usually** a safe answer to choose. E.g., for *bug detecting* problem, we could say **YES** to be safe because we want no false error alarm. And for *program verification* problem, we could say **NO** to be safe because we don't want any potential errors to be omitted. And for general questions like the range of an int variable, we can say it may be any number to be safe. **Note that safe does not mean correct**.



However, both of the above answers are obviously not useful enough, i.e., we need more **informative** answers. To achieve the goal, we need to try to be more sure about each question. **And that's what all of our struggles lies in**. 



# Normalization

Flatten nested expressions, using fresh variables, e.g.

```C++
x = f(y+3)*5;

// after normalization

t1 = y+3;
t2 = f(t1);
x = t2*5;
```



# Summary

- Target of SPA is to **answer questions** about program points and program, and they could be "yes-or-no" questions and general questions.

- Being **correct** is not easy, and thus we accept **approximation** answers.

- Safe or **Conservative** is a basic and must requirement for approximation answers.
- Except from **soundness**, we also want more **informative** or precise answers. That's will usually consume more resources like time and memory.

# Reference

- [*Static Program Analysis*](https://cs.au.dk/~amoeller/spa/)

- [SPA Slide 1](https://cs.au.dk/~amoeller/spa/1-TIP.pdf)

