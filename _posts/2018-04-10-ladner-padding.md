---
layout:     post
title:      Ladner's Theorem Proof Explained
date:       2018-04-10
summary:    What is between P and NP-hard?
categories: informatics logic P NP complexity 
---

## Ladner's Theorem

> If $$P \ne NP$$ then there is a problem which is neither in $$P$$ nor $$NP$$-hard.

In the rest of the article we'll proof this theorem following Russell Impagliazzo's idea of padding. In fact, this will be an extended explanation of the proof printed in Arora and Barak's *Computational Complexity*.

## Theoretical Setup

A **problem** is a set of natural numbers, containing the encodings of the problem instances with positive answer. For example the problem for detecting is a number is prime or not will be represented by the set of primes.

A **program** is a nutural number - the encoding of an instance of some fixed computational model, e.g. a Turing machine. The encoding is bijection between $$\mathbb{N}$$ and the set of all programs. This way each natural number is a code of a program and with $$P_i$$ we'll denote the program with code $$i$$.

For input $$x$$ a program $$P$$ **outputs** the value $$P(x)$$.

A program P **solves a problem** if $$\forall x (P(x)=1 \leftrightarrow x \in P)$$. 

$$Time_i(x)$$ is the execution time (number of steps till termination) for the program $$P_i$$ with input $$x$$. If $$P_i(x)$$ does not halt  $$Time_i(x) = \infty$$.

A problem can be solved in time T if there is a program that solves it in time T.

$$P$$ is the set of all problems that can be solved in time $$O(n^c)$$ where $$c$$ is a constant.

$$NP$$ is the set of all problem that can be solvedin time $$O(n^c)$$ given a certificate of polynomial length. 

Problem A can be **reduced** to problem B if given a solution for A we can solve B.

$$NP$$-hard is the set of all problems $$A$$ such that every problem $$B \in NP$$ can be reduced to $$A$$.

$$NP$$-complete is the set $$NP \cap NP-hard$$.

The **bounded minimization operator** is defined as:

$$ \mu (i < n) [S(i)] = \begin{cases} 
      \text{the smaller } i < n \text{ such that } S(i) & \text{if such exists} \\
      n & \text{otherwise}
   \end{cases} $$
   

## Idea

The proof will follow this idea: 
1. We'll take a problem which is NP-complete.
1. We'll make the problem "easier".
1. We'll show that the new problem is in NP, but is not in P nor NP-complete.

## SAT: An NP-complete problem

We'll take the problem SAT as a typical example for NP-complete problem. Actually we do that only for the sake of clarity. It doesn't matter which NP-complete problem we pick.


## Padding: Making the problem easier

To make the problem easier we'll use a techique called "padding". Let $$ pad(x) = x \circ 11 \dots 11 $$ is a function which given an input x for SAT appends a certain number of 1s at the end. We define the new problem A as follows

$$ A = \{ pad(x) | x \in SAT \} = \{ x \circ 11\dots11 | x \in SAT \} $$

So $$ pad $$ is a bijection between the sets $$SAT$$ and $$A$$ and with $$ pad^{-1} $$ we'll denote the inverse. 
This way we artificially enlarge the input size, which will reduce the running time of the algorithms solving SAT (because the running time is defined as a function of the input). For example, suppose 

$$A = \{ x \circ 1^{2^{|x|}} | x \in SAT \}$$

This way if $$x \in A$$ and the length of x is n, only $$log(n)$$ of the input is actually useful. We know we can solve SAT in $$ O(2^n) $$ time by a bruteforce algorithm $$Br$$. Now let's consider the following solution of problem A:

Given input X.
1. $$Y = pad^{-1} (X)$$ (We remove the padding)
2. return $$Br(Y)$$ (run the bruteforce algorithm for SAT). 

Since we have only $$O(log(n))$$ of the input left at step 2, the bruteforce algorithm will work in time $$O(2^{O(log(n))}) = O(n^c) \in P$$. This way we made A so easy that it becames in $$P$$. 

The goal of the rest of the proof will be to find the right amount of 1s to pad with, so A will be neither in $$P$$ nor in $$NP$$-complete. More formally we'll try to find a function H such that:

$$ pad(x) = x \circ 1^{n^{H(n)}} $$

## What should H(n) be?
Let's look at the rest of the proof and figure out what do we need from $$H(n)$$ so the proof will work.
We need to show three things:

1. $$A \in NP$$ ,
1. $$A \notin P$$ , 
1. $$A \notin NP$$-hard.

### A is in NP
Intuitively that should be true, because SAT is in $$NP$$ and A is an easier version of SAT, so it should also be in NP. Here's a more formal proof:

By the definition of NP we have $$A \in NP \Leftrightarrow $$ given a polynomial-length certificate A can be solved in polynomial time. Let the certificate be the truth assignments of the variables in the underlying SAT instance of A. Since the variables are at most $$O(n)$$, the certificate is also $$O(n)$$. We can check in poly-time if this truth assignment satisfies the formula from the input.
  
### A is not in P
Let's conduct a proof by contradiction. Suppose $$A \in P $$. So there exists a program $$Pr$$ which solves A in poly-time.

We can solve SAT by the following algorithm:

Given $$x$$ - input for SAT.
1. $$ y = pad(x) $$ 
2. return $$ Pr(y)$$. 

Step 2 takes polynomial time. If step 1 also takes polynomial time we'll have a polynomial algorithm for SAT $$\Rightarrow SAT \in P$$ which will be a contradiction. Let $n$ be the length of $x$. Step 1 takes time $$O(n^{H(n)})$$ because $$H(n)$$ is the number of 1s we need to pad with. So we want $$O(n^{H(n)}) \in P \Rightarrow $$ we want $$H(n)$$ to be bounded by a constant. Furthermore we need $H$ to be a **poly-time computable function**.

Let's repeat that: $$H(n)$$ should be such a function that 

$$ \exists n_0, c ( A \in P \Rightarrow \forall n > n_0 H(n) < c ) $$

### A is not in NP-complete
Now we have $$A \notin P$$. We want to show that $$A \notin NP$$-complete. Since we already have $$A \in NP$$, this is equivalent to $$A \notin NP$$-hard. That means there is no polynomial reduction from SAT to A. Suppose there is such reduction $$R$$. Now we can solve SAT this way:

Given input X.
1. If $$\vert x\vert < c$$ solve X by bruteforce ($$c$$ is some constant).
1. $$Y \leftarrow R(X)$$ (reduce X to input Y for the problem A) 
1. $$X \leftarrow pad^{-1}(Y)$$ (remove the padding)
1. Repeat 1.

To find a contradiction, the length of X should be monotonically decreasing with the iterations. The loop will repeat linear number of times and we'll end up with a polynomial solution for SAT which is a contradiction.

Since this reduction take polynomial time it will produce a polynomial output $$ \Rightarrow \vert Y\vert < \vert X \vert ^c$$ for some constant $$c$$. If $$Y = Z \circ 111\dots1$$ and $$\vert Z\vert = n$$ we have $$\vert Y\vert = n + n^{H(n)}$$. In other word we want the following implication to be true

$$ \forall n (n + n^{H(n)} < \vert X \vert ^c  \Rightarrow n < \vert X \vert)$$

This will be true if $$H(n)$$ is not bounded by a constant, i.e. $$H(n)$$ tends to infinity with $$n$$. 
So to proof this part we need:

$$A \notin P \Rightarrow \forall M \exists n_o \forall n > n_0 (H(n) > M)$$

## Finding such H

Now we need to find function H which satisfies:

1. $$\exists n_o, c ( A \in P \Rightarrow \forall n > n_0 H(n) < const )$$

2. $$A \notin P \Rightarrow \forall M \exists n_o \forall n > n_0 (H(n) > M)$$

3. H is computable

4. H is poly-time computable

Actually $$A \in P \Leftrightarrow \exists i$$ such that the program $$P_i$$ (the program with code $$i$$) solves A for polynomial number of steps. So we need to define $$H(n)$$ in terms of $$i$$ and wheather the i-th program solves $$A$$ in polynomial time. Our first attempt will be something like this:

$$ H(n) = \mu (i < n) \left[ P_i \text{ halts in polynomial time and } P_i \text{ solves } A \right] $$

So if $$A \in P$$ then there will be such $$i$$ that $$P_i$$ solves A and $$\forall n > i (H(n) = i)$$ which will satisfy (1). 
If $$A \notin P$$ then $$ \{ i | P_i \text{ halts in polynomial time and } P_i \text{ solves } A\} = \varnothing$$ and $$\forall n (H(n) = n)$$ which satisfies (2).

### Making H computable 

$$H$$ needs to be poly-time computable function. Currently computing $$H$$ involves checking if a program halts in polynomial time and if it solves A, which is not computable.

Let's first make $$H$$ computable.

$$ P_i \text{ halts in polynomial time } \rightsquigarrow \forall x (P_i(x) \text{ halts in time } i\vert x \vert^i ) $$

That provides a stronger constraint for $$P_i$$. It should not only halt in polynomial time but in time less that $$i\vert x \vert^i$$. Actually this is not a limitation because if there is a program $$P_i$$ that solves A in time $$n^c$$ there is $$j > c$$ such that the program $$P_j$$ computes the same function as $$P_i$$ and runs in the same time bounds. So $$P_j$$ will run in time $$jn^j$$.

We are getting closer to computable but still the part with $$\forall x$$ is a problem. Let's bound x:

$$\forall x (\dots) \rightsquigarrow  \forall x , |x| < n (\dots) $$

The second part of H is:

$$P_i \text{ solves } A \rightsquigarrow \forall x (P_i(x)=1 \Leftrightarrow x \in A ) \rightsquigarrow \forall x<n (P_i(x)=1 \Leftrightarrow x \in A ) $$

Now we can restate H so it's computable:

$$ H(n) = \mu (i < n) \left[ \forall x, |x| < n \left( Time_i(x) < i\vert x\vert^i \text{ } \& \text{ } P_i(x) = 1 \Leftrightarrow x \in A \right) \right] $$

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

Finally we have:


$$ H(n) = \mu (i < \log \log n) \left[ \forall x, \vert x \vert < \log n \left( Time(P_i(x)) < i\vert x\vert^i \text{ } \& \text{ } P_i(x) = 1 \Leftrightarrow x \in A \right) \right] $$

## References 

1. [Ladner. On The structure of Polynomial Time Reducibility, 1975](https://dl.acm.org/citation.cfm?id=321877)
1. Arora. Barak. Computational Complexity: A Modern Approach. Chapter 3.3. Cambridge University Press, 2009 
