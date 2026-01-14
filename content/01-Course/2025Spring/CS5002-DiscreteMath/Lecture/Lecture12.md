---
tags:
  - neu/CS5002
description: Baye Theorem
pageorder: 12
link: "[[CS5002note12_0220.pdf]]"
---


## Standard Equation
#### Bayes' theorem
$$P(A∣B)=\frac{P(B∣A)⋅P(A)​}{P(B)}$$
#### Conditional Probability
$$P(A|B)=\frac{P(A\cap B)}{P(B)}$$
$$P(A\cap B)=P(A|B)⋅{P(B)}$$
#### Law of total probability
$$P(A)=P(A \cap B) + P(A \cap \bar{B})$$
#### Binomial probability equation
$$P(X = k) = \binom{n}{k} p^k (1 - p)^{n - k}$$
##### Combinations
$$C(n, r)=\frac{n!}{(n-r)!r!}$$
### Example 1
Testing for Covid:
$V$: have Covid
$\bar V$: don't have Covid
$T$: test positive
$\bar T$: test negative

Give: 
$P(T|V)=0.98$ → $P(\bar T|V)=0.02$
$P(\bar T|\bar V)=0.99$ → $P(T|\bar V)=0.01$
$P(V)=0.01$→$P(\bar V)=0.01$

$P(V|T)=\frac {P(T|V)⋅P(V)}{P(T)}=\frac{0.98⋅0.01}{0.0197}=0.4975$

$P(T) = P(T \cap V) + P(T \cap \bar{V}) = P(T \mid V) \cdot P(V) + P(T \mid \bar{V}) \cdot P(\bar{V})$
$=0.98⋅0.01 + 0.01⋅0.99=0.0197$
### Example 2
> Q: Toss a coin twice, x = sum of the outcome of the coin
> > 1. What's the probability of getting two head?
> > 2. What's the probability of getting one head and one tail?
> > 3. What's the probability of getting two tail?
> > Head = 1; Tail = 0
- $C(n,0)=1$ (choosing nothing from n items always has one way).
- $C(n,n)=1$


A1: What's the probability of getting two tails?
$$P(X = 0) = \binom{2}{0} p^2 (1 - p)^{0}=\frac{1}{4}$$

A2: What's the probability of getting 1 head and 1 tail?
$$P(X = 1) = \binom{2}{1} p^{1} (1 - p)^{1}=\frac{1}{2}$$A3: What's the probability of getting 2 heads?
$$P(X = 2) = \binom{2}{2} p^0 (1 - p)^{2}=\frac{1}{4}$$
