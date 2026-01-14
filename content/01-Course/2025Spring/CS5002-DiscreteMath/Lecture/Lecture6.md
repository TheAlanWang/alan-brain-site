---
tags:
  - neu/CS5002
description: sets
date: 2025-01-27
pageorder: 6
---

> Date: Jan 27 2025
### Nested quantifier
`∀x∀y x+(y+z)=(x+y)+z` True

- `∀x∃y, x + y = 0` True
- `∃x∀y, x + y ≠ 0` False
Conclusion: negate a statement with several quantifiers, we still apply the same rules as with one quantifier
## Sets
- Set = **unordered** group of  elements
A set contains an element a: `a ∈ A`
#### Set-builder notation
$${x∣x\; is\; an\; even \;number}$$
#### Important sets
###### ℕ (Natural Numbers)
set of positive integers
- {1,2,3,…}
###### ℤ⁺ (Positive Integers)
- {0,1,2,…}
###### ℤ (Integers)
- {…,−2,−1,0,1,2,…}
###### F (Floating numbers)
- 0.000000001 (limited)
###### ℚ (Rational Numbers)
set of all numbers that can be expressed as a fraction 
- {$\frac{p}{q}| \; p∈ℤ,q∈ℤ,q≠0$}
- $\frac{1}{3}=0.3333333...(unlimited)$
###### ℝ (Real Numbers) 
- include both *rational* and *irrational* numbers (π)
###### ℂ (Complex Numbers)
- $i^2=−1$

$$ℕ∈ℤ∈F∈ℚ∈ℝ∈ℂ$$
#### Compare sets
###### How to compare sets:
Two sets are equals if and only if they have same elements.

###### cardinality
counter of distinct element is a set
set A = {1, 2, 3}, the **cardinality** of A, |A| is 3

|∅| = 0
A ∈ B: A is an element of the set B
A ⊆ B: A is subset of B

$|\;P(A)\;| = 2^{|A|}$
{0, 1, 2}: $2^3=8$
{0}, {1}, {2}, {0, 1}, {0, 2}, {1, 2}, {0, 1, 2}, {∅}
The collection of items P(S) = set of all subsets of A

#### Example
$$∀x∈ℚ (x^2≥0)\;True$$
$$∀x∈ℂ (x^2≥0)\;False$$
$$∃x∈ℤ (x^2=0)\;True$$