---
tags:
  - neu/CS5002
date: 2025-03-28
weight: 17
---
## Lecture 17: Induction

**Statement:**
$$\exists n > k,\ \text{such that}\ n^2 > Cn,\ \forall c,k$$
Choose $n > 0,\ n > c,\ n > k \Rightarrow n > \max(c,k)$, as long as you choose.

---

### Proof technique: Induction
**Example 1:**
Prove:
$$
1 + 2 + 3 + 4 + \ldots + n = \frac{n(n+1)}{2}
$$

**Base case:** $P(1)$
**Induction step: Assume** $$P(n)$$ is true, prove $P(n+1)$:

**Left side:*$1 + 2 + 3 + \ldots + n + (n+1) = \frac{n(n+1)}{2} + (n+1)$

Factor:$= (n+1)\left(\frac{n}{2} + 1\right) = (n+1)\left(\frac{n+2}{2}\right) = \frac{(n+1)(n+2)}{2}$

**Right side:**$\frac{(n+1)(n+2)}{2}$

**Conclusion:**
We showed the proposition holds for $n = 1$ and if it’s true for some $n$, it’s true for $n+1$. Therefore, by induction, the proposition is true for any $n$.

---
**Example 2:**
Prove:
$$
1^2 + 2^2 + 3^2 + \ldots + n^2 = \frac{n(n+1)(2n+1)}{6}
$$
**Induction step:**
$1^2 + 2^2 + \ldots + n^2 + (n+1)^2 = \frac{n(n+1)(2n+1)}{6} + (n+1)^2$

Factor:$= (n+1)\left(\frac{n(2n+1)}{6} + (n+1)\right)= (n+1)\left(\frac{2n^2 + 7n + 6}{6}\right)= \frac{(n+1)(n+2)(2n+3)}{6}$

**Right side:**
$$
\frac{(n+1)(n+2)(2(n+1)+1)}{6} = \frac{(n+1)(n+2)(2n+3)}{6}
$$

---

**Example 3: Tower of Hanoi**

**Question:** Move $n$ disks from rod1 to rod3.

Steps:
- Move $n-1$ disks from rod1 to rod2: $M_{n-1}$ moves
- Move largest disk from rod1 to rod3: $1$ move
- Move $n-1$ disks from rod2 to rod3: $M_{n-1}$ moves

Total moves: $M_n = 2M_{n-1} + 1$

**Show:** $M_n = 2^n - 1$
Proof: $M_n = 2M_{n-1} + 1 = 2(2^{n-1} - 1) + 1 = 2^n - 2 + 1 = 2^n - 1$
