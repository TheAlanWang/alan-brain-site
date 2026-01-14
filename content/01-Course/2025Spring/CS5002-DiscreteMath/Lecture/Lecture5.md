---
tags:
  - neu/CS5002
description: quantifier
date: 2025-01-23
weight: 5
---
type the universal quantifier(∀\for all) or the existential quantifier (∃\exists)
### Quantifier `∀` `∃`
`∀`: for all
`∃`: there exists

### Domain P(x) → $x_1, x_2, x_3$
The domain of f(x), domain = the set of **all possible input values**
$$∀, P(x)≡P(human1)∧P(human2)∧P(human3)$$

∃ x, height of x is different than 6 ft


### Question 1
1, TRUE
$$∀x,\; x>5$$
2, TRUE
3, FALSE

### Question 2 Negate
Q1 
$$¬(∀x, x > 5) ≡ ∃x,\; x≤5$$
Q2
$$¬(∃x,\;x^2<2)≡∀x,\;x^2≥2$$

### Exercise1
$$∀x(\;P(x) ∧ Q(x)\;)≡∀x\;P(x) ∧ ∀x\;Q(x)$$

## Question to Poof:
$$To\;Proof:\;∀x\;P(x) ∧ ∀x\;Q(x)≡∀x(\;P(x) ∧ Q(x)\;)$$
# Proof:
$$method1: ∀x\;P(x)=True$$
$$So: P(-1)=P(0)=P(1)=P(2)=P(3)...=True$$

$$method2:∀x\;Q(x)=True$$

$$So:Q(1)=Q(2)=Q(3)...=True$$

$$method1∧method2=P(1)∧Q(1)≡P(2)∧Q(2)≡True$$

$$method1∧method2=P(x) ∧ Q(x)≡True$$

$$≡∀x(\;P(x) ∧ Q(x)\;)$$
#### Order Matters
###### Equation 1
$$∀x\;∃y\;(x+y)=0$$
Explain:
For every X, there must be a y such that x+y=0, **True**

###### Equation 2
$$∃y\;∀x\;(x+y)=0$$
Explain:
Is there some Y, no matter every X can such that x+y=0, **False**
