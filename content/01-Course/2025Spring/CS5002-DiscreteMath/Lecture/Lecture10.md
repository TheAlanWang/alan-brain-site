---
tags:
  - neu/CS5002
description: permutation
pageorder: 10
---

> Date: Feb 10 2025, Monday

## Review:
- P(S) = 1 Sample space
- P(A) ≥ 0
- P(A U B) = P(A) + P(B) if A and B are two disjoint events
-  $P(A) = \frac{|\;A\;|}{|\;S\;|}$

## Lecture:
### Permutation
>A **permutation** is an arrangement of objects **in a specific order**
$$P(n,r)=\frac{n!}{(n-r)!}$$
#### Sample 1
>Q1. Put "A" "B" "C" "D" in `[_ _ _ _]`, probability of having a C occurring before the letter D?

`|S| = 4 * 3 * 2 * 1 = 24` 

##### How to get |A|? 
<u> n </u> <u> n-1 </u> <u> n-2</u> ... <u> n-(r-1)</u>
- n: Total number of elements available.
- r: Number of elements being chosen or arranged. 

In this case, 
- n = 4 (cause A, B ,C ,D)
- r = 4
- last `_` = n - r + 1 = 4 - 4 + 1 = 1, which has 1 choose left

##### To derive the *Permutation* formula
$| A | = n * (n-1) * (n-2) * (n-3) * (n - r + 1 )$
$| A | = n * (n-1) * (n-2) * (n-3) * (n - r + 1 )*\frac{(n-r)(n-r-1)...(2)(1)}{(n-r)(n-r-1)...(2)(1)}$
$|A|=\frac{n(n-1)(n-2)(n-3)(n-r+1)(n-r)(n-r-1)...(2)(1)}{(n-r)(n-r-1)...(2)(1)}$
$|A|=\frac{n!}{(n-r)!}$
$|A|=P(n, r)$ → P is permutation

> continue Q1. Put "A" "B" "C" "D" in `[_ _ _ _]`, probability of having a C occurring before the letter D?

$P(A) = \frac{|\;A\;|}{|\;S\;|}=\frac{|\;A\;|}{P(4,4)}=\frac{|\;A\;|}{24}$
$|A|$ → # of element for which C occurs before D
Scenario 1: <u>__</u> <u>__</u> <u>__</u> <u>_C_</u> → 0 possibility
Scenario 2: <u>__</u> <u>__</u> <u>_C_</u> <u>__</u> → 2 possibility → ABCD, BACD
Scenario 3: <u>__</u> <u>_C_</u> <u>__</u> <u>__</u> → 4 possibility → ACBD, BCAD, BCAD, ACBD
Scenario 4: <u>_C_</u> <u>__</u> <u>__</u> <u>__</u> → 6 possibility →  $P(3,3)=3!=6$

$|A|=12, |S| = 24$  → probability is $\frac{12}{24}=0.5$ 

#### Sample 2
> `HELLLO` What is the number of distinct words we can get by permuting the letters?

$|S|=P(6,6)=6*5*4*3*2*1=720$
$LLL = 3!=6$, because the letter L appear 3 times
$$number\;of\;distinct = 720/6 = 120$$
#### Sample 3
> There are 5 letters, in alphabetic oder.
> 	$1^{st}:$ AGILN 
> 	$2^{nd}:$ AGINL
> 	... 
> 	$120^{th}:$ NLIGA
> 
> Q3.1 <u>_A_</u> <u>_L_</u> <u>_I_</u> <u>_G_</u> <u>_N_</u>  is the $M^{th}$ word, Find M
> Q3.2 Determine the $87^{th}$ word in the set of word
> Q3.3 Pick one of these word at random begin with a vowel and end with a consonant

##### Q3.1 ALIGN is the $M^{th}$ word, Find M?

Inside: <u>_A_</u> <u>__</u> <u>__</u> <u>__</u> <u>__</u> → 4! = 24
<u>_A_</u> <u>_G_</u> <u>__</u> <u>__</u> <u>__</u> → 3! = 6
<u>_A_</u> <u>_I_</u> <u>__</u> <u>__</u> <u>__</u> → 3! = 6
<u>_A_</u> <u>_L_</u> <u>_G_</u> <u>__</u> <u>__</u> → 2! = 2
6+6+2 = 14
Before ALIGN is 14, so Align is $15^{th}$

##### Q3.2 Determine the $87^{th}$ word in the set of word

<u>_A_</u> <u>__</u> <u>__</u> <u>__</u> <u>__</u> 4! = +24
<u>_G_</u> <u>__</u> <u>__</u> <u>__</u> <u>__</u> 4! = +24
<u>_I_</u> <u>__</u> <u>__</u> <u>__</u> <u>__</u> 4! = +24 →sum = 72 < 87, but add 4! is > 87
<u>_L_</u> <u>__</u> <u>__</u> <u>__</u> <u>__</u>  start from 72
<u>_L_</u> <u>_A_</u> <u>__</u> <u>__</u> <u>__</u>  3! = +6 → sum = 78
<u>_L_</u> <u>_G_</u> <u>__</u> <u>__</u> <u>__</u>  3! = +6 → sum = 84(84<87)
<u>_L_</u> <u>_I_</u> <u>_A_</u> <u>__</u> <u>__</u>  2! = +2 → sum = 86
<u>_L_</u> <u>_I_</u> <u>_G_</u> <u>_A_</u> <u>_N_</u>  +1 → sum = 87

##### Q3.3 Pick one of these word at random begin with a vowel and end with a consonant
vowel = `A, I`
consonant = `G, N, L`
<u>_A_</u> <u>_I_</u> <u>_I_</u> <u>_I_</u> <u>__</u> →$3!*3=18$
<u>_I_</u> <u>_A_</u> <u>_A_</u> <u>_A_</u> <u>__</u> →$3!*3=18$
$\frac{18+18}{120}=0.3$

### Combinations
Whenever order does not matter, we will look at a different quantity.(for example Poker)

There are 5 spots, can put in 5 Poker cards in r spots
	<u>__</u> <u>__</u> <u>__</u> <u>__</u> <u>__</u>
- r cards → r! permutation
- cards in hand do not care the order

$\frac{P(n,r)}{r!}$ not overcount the same hand many times (order doesn't matter)

$$C(n, r)=\frac{n!}{(n-r)!r!}$$
