---
layout:     post
title:      Ladner's Theorem Proof Explained
date:       2018-02-23
summary:    What is between P and NP-hard?
categories: informatics logic P NP complexity 
---

## Ladner's Theorem

> If $$P \ne NP$$ then there is a problem which is neither in $$P$$ nor $$NP$$-hard.

## Theoretical Setup

**Problem** is a set of numbers. 

Given an input $$x$$ and program $$P$$ it **outputs** the value $$P(x)$$.

A program P **solves a problem** if $$\forall x (P(x)=1 \leftrightarrow x \in P)$$. 

A program can be solved in time T if there is a program that solves the problem in time T.

$$P$$ is the set of all problems that can be solved in time $$O(n^c)$$ where $$c$$ is a constant.

$$NP$$ is the set of all problem that can be solvedin time $$O(n^c)$$ given a certificate of polynomial length. 

Problem A can be **reduced** to problem B if given a solutoin for A we can solve B.

$$NP$$-hard is the set of all problems $$A$$ such that every problem $$B \in NP$$ can be reduced to $$A$$.

$$NP$$-complete is the set $$NP \cap NP-hard$$.

The *minimization operator* is defined as:

$$ \mu (i < n) [S(i)] = \begin{cases} 
      \text{the smaller } i < n \text{ such that } S(i) & \text{if such exists} \\
      n & \text{otherwise}
   \end{cases} $$
   

## Proof idea

The proof will follow this idea: 
1. We'll take a problem which is NP-complete.
1. We'll make the problem "easier".
1. We'll show that the new problem is in NP, but is not in P nor NP-complete.

## SAT: An NP-complete problem

We'll take the problem SAT as a typical example for NP-complete problem. Actually we do that only for the sake of clarity. It doesn't matter which NP-complete problem we pick.


## Padding: Making the problem easier

To make the problem easier we'll use a techique called "padding". We define the padded problem A as follows

$$ A = \{ x \circ 11\dots11 | x \in SAT \} $$

So we just append some number of 1s at the end of each SAT example. This way we artificially enrage the input size, which will reduce the running time the algorithms solving SAT (because the running time is defined as a function of the input). For example, suppose 

$$A = \{ x \circ 1^{2^{|x|}} | x \in SAT \}$$

This way if $x \in A$ and the lenfth of x is n, only $$log(n)$$ of the input is actually useful to solve the problem. We know we can solve SAT is $$ O(2^n) $$ time by bruteforce. Now let's consider to following solution to the problem A:

1. Given input X, remove the padding.
2. Run the bruteforce algorithm for SAT. 

Since we have only $$O(log(n))$$ of the input left at step 2, the bruteforce algorithm will work in time $$O(2^{O(log(n))}) = O(n^c) \in P$$. This way we made A so easy it became in $$P$$. 

The goal of the rest of the proof will be to find the right amount of 1s to pad with, so A will be neither in $$P$$ nor in $$NP$$-complete. More formally we'll try to find a function H such that:

$$A = \{ x \circ 1^{n^{H(n)}} | x \in SAT \& |x|=n \}$$

## What should H(n) be?
Let's look at the rest of the proof and figure out what do we need from H(n) so the proof will work.
We need to show three things:

1. $$A \in NP$$ ,
1. $$A \notin P$$ , 
1. $$A \notin NP$$-complete.

### A is in NP
Intuitively that shuold be true, because SAT is in $$NP$$ and A is an easier version of SAT, so it shuold also be in NP. Here's a more format proof:

By the definition of NP we have $$A \in NP \Leftrightarrow $$ given a polynomial-length certificate A can be solved in polynomial time. Let the certificate be the truth assignments of the variables in the underlying SAT instance of A. Since the variables are at most $$O(n)$$, the certificate is also $$O(n)$$. We can check in poly-time if this truth assignment satisfies the formula from the input.
  
### A is not in P
Let's conduct a proof by contradiction. Suppose $$A \in P $$. 

We can solve SAT by the following algorithm:
Given $$x$$ - input for SAT.
1. $$y \leftarrow x \circ 1^{n^{H(n)}}$$ where $$n$$ is the length  of $$x$$
2. Run the polynomial algorithm for A to check if $$y \in A$$. 

Step 2 takes polynomial time. If step 1 also takes polynomial time we'll have a polynomial algorithm for SAT $$\Rightarrow SAT \in P$$ which will be a contradiction. Step 1 takes time $$O(n^{H(n)})$$.  So we want $$O(n^{H(n)}) \in P \Rightarrow $$ we want $$H(n)$$ to be bounded by a constant.

Let's repeat that: $$H(n)$$ should be such a function that 

$$A \in P \Rightarrow \forall n > n_0 H(n) < const$$

### A is not in NP-complete
Now we have $$A \notin P$$. We want to show that $$A \notin NP$$-complete. Since we already have $$A \in NP$$, this is equivalent to $$A \notin NP$$-hard. That means there is no polynomial reduction from SAT to A. Suppose there is such. Now we can solve SAT this way:

Given input X.
1. If $$\vert x\vert < c$$ solve X by bruteforce ($$c$$ is some constant).
1. Reduce X to input Y for the problem A.
1. Set X = Y without padding 
1. Repeat 1.


If we are reducing the length of X on step 3, the loop will repeat linear number of times and we'll end up with a polynompial solution for SAT. This will be a  contradiction.

Since this reduction take polynomial time it will produce a polynomial output $$ \Rightarrow \vert Y\vert < \vert X \vert ^c$$ for some constant $$c$$. If $$Y = Z \circ 111\dots1$$ and $$\vert Z\vert = n$$ we have $$\vert Y\vert = n + n^{H(n)}$$. In other word we want the following implication to be true

$$ \forall n (n + n^{H(n)} < \vert X \vert ^c  \Rightarrow n < \vert X \vert)$$

This will be true if $$H(n)$$ is not bounded by a constant, i.e. $$H(n)$$ tends to infinity with $$n$$. 
So to proof this part we need:

$$A \notin P \Rightarrow \forall M \exists n_o \forall n > n_0 (H(n) > M)$$

## Finding such H

Now we need to find function H which satisfies:

1. $$A \in P \Rightarrow \forall n > n_0 H(n) < const$$

2. $$A \notin P \Rightarrow \forall M \exists n_o \forall n > n_0 (H(n) > M)$$

Actually $$A \in P \Leftrightarrow \exists i$$ such that the program $$P_i$$ (the program with code $$i$$) solves A for polynomial number of steps. So we need to define $$H(n)$$ in terms of $i$ and wheather the i-th program solves $A$ in polynomial time. Our first attempt will be something like this:

$$ H(n) = \mu (i < n) \left[ P_i \text{ halts in polynomial time and } P_i \text{ solves } A \right] $$

So if $$A \in P$$ then there will be such $$i$$ that $$P_i$$ solves A and $$\forall n > i (H(n) = i)$$ which will satisfy (1). 
If $$A \notin P$$ then $$ \{ i | P_i \text{ halts in polynomial time and } P_i \text{ solves } A\} = \varnothing$$ and $$\forall n (H(n) = n)$$ which satisfies (2).

We are almost done! Just need to fix some more stuff..

### Making H computable 

We forgot a small details. $$H$$ needs to be poly-time computable function. Currently computing $$H$$ involves checking if a program halts in polynomial time and if it solves A. That unfortunately is even far from computable.

Let's first make $$H$$ computable.

$$ P_i \text{ halts in polynomial time } \rightsquigarrow \forall x (P_i(x) \text{ halts in time } i\vert x \vert^i ) $$

That provides a stronger constraint for $$P_i$$. It should not only halt in polynomial time but in time lestt that $$i\vert x \vert^i$$. Actually this is not a limitation because if there is a program $$P_i$$ that solves A in time $$n^c$$ there is $$j > c$$ such that the program $$P_j$$ computes the same function as $$P_i$$ and runs in the same time bounds. So $$P_j$$ will run in time $$jn^j$$.

We are getting closer to computable but still the part with $$\forall x$$ is a problem. Let's bound x:

$$\forall x (\dots) \rightsquigarrow  \forall x < n (\dots) $$

The second part of H is:

$$P_i \text{ solves } A \rightsquigarrow \forall x (P_i(x)=1 \Leftrightarrow x \in A ) \rightsquigarrow \forall x<n (P_i(x)=1 \Leftrightarrow x \in A ) $$

Now we can restate H so it's computable:

$$ H(n) = \mu (i < n) \left[ \forall x < n \left( Time(P_i(x)) < i\vert x\vert^i \text{ } \& \text{ } P_i(x) = 1 \Leftrightarrow x \in A \right) \right] $$

### Making H poly-time computable

Currently to compute $$H(n)$$ we'll need to simulate some programs $$P_i$$ for $$i\vert x\vert^i$$ steps. Since $$ i < n \text{ }\&\text{ } \vert x \vert < n $$ these are $$O(n^n)$$ steps. Let's change the constraints to $$i < \alpha(n)$$ and $$\vert x \vert < \beta(n)$$. Then the complexity will be 

$$ \alpha(n) 2^{\beta(n)} \alpha(n) \beta(n) ^ {\alpha(n)}  \in P$$

We can break this down to $$ 2^{\beta(n)} \in P $$ and $$ \beta(n) ^ {\alpha(n)}  \in P $$. Having this in mind it's not hard to find this solution for $$\alpha$$ and $$\beta$$:

$$ \alpha(n) = \log \log (n) $$

$$ \beta(n) = \log (n) $$

Let's check: $$ 2^{\beta(n)} = 2 ^ {\log n} = n \in P $$

$$ \lim _ {n \rightarrow \infty} \frac { \beta(n) ^ {\alpha(n)} } {n^c} = 
\lim _ {n \rightarrow \infty} \frac { \log { \beta(n) ^ {\alpha(n)} } } { \log {n^c} } = 
\lim _ {n \rightarrow \infty} \frac { \alpha(n) \log  \beta(n) } { c \log {n} } =
\lim _ {n \rightarrow \infty} \frac { (\log \log (n) )^2 } { c \log {n} } =
\lim _ {n \rightarrow \infty} \frac { (\log n )^2 } { c n } = 0$$

$$ \Rightarrow \beta(n) ^ {\alpha(n)}  \in P  $$

And finaly we have:


$$ H(n) = \mu (i < \log \log n) \left[ \forall \vert x \vert < \log n \left( Time(P_i(x)) < i\vert x\vert^i \text{ } \& \text{ } P_i(x) = 1 \Leftrightarrow x \in A \right) \right] $$

## References 

[Ladner. On The structure of Polynomial Time Reducibility](https://dl.acm.org/citation.cfm?id=321877)
