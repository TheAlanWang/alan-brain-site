---
tags:
  - neu/CS5002
description: truth table
date: 2025-01-16
weight: 4
---
### Truth table

|  p  |  q  | p ∧ q (and) | p ∨ q (or) | p ⊕ q (exclusive or) |
| :-: | :-: | :---------: | :--------: | :------------------: |
|  T  |  T  |      T      |     T      |          F           |
|  T  |  F  |      F      |     T      |          T           |
|  F  |  T  |      F      |     T      |          T           |
|  F  |  F  |      F      |     F      |          F           |
Conjunction event: AND $∧$
Disjunction event: OR $∨$

### Conditions (p → q)
**Only False, when p is T, q is F**

|  p  |  q  | ¬p  | ¬q  | p ∨ q | ¬(p ∨ q) | q → p | p → q | ¬p → ¬q | ¬q → ¬p | ¬p → q | ¬p ∧ ¬q |
| :-: | :-: | :-: | :-: | :---: | :------: | :---: | :---: | :-----: | :-----: | :----: | :-----: |
|  T  |  T  |  F  |  F  |   T   |    F     |   T   |   T   |    T    |    T    |   T    |    F    |
|  T  |  F  |  F  |  T  |   T   |    F     |   T   |   F   |    T    |    F    |   F    |    F    |
|  F  |  T  |  T  |  F  |   T   |    F     |   F   |   T   |    F    |    T    |   T    |    F    |
|  F  |  F  |  T  |  T  |   F   |    T     |   T   |   T   |    T    |    T    |   T    |    T    |
### Logical Law
#### De Morgan's Law
- "Not A and B" is equivalent to "Not A or Not B"
$$¬(A ∧ B) ≡ ¬A ∨ ¬B$$
- "Not A or B" is equivalent to "Not A and Not B"
$$ ¬(A ∨ B) ≡ ¬A ∧ ¬B $$
#### Contrapositive Law
- Only `p`is True, which means `¬p` is False and q is false, `¬p ∨ q` is False.
$$p → q \; ≡ \; ¬q → ¬p \;≡\; ¬p ∨ q$$
#### Double Negative Law
$$¬(¬p) ≡ p$$
#### Commutative Law
- for OR:
$$p ∨ q ≡ q ∨ p$$
- for AND:
$$p ∧ q ≡ q ∧ p$$
### Question
##### Question 1:
$$Proof:p ∧ (p→q) \;≡\; p∧q $$
$$p∧(p→q)\;≡\; p∧(¬p∨q)\;≡\; (p∧¬p)∨(p∧q)\;≡\;p∧q$$
notes: Because $(p∧¬p)$ is False, $False ∨ (p ∧ q)$ depends on $(p ∧ q)$ 

#####  Question 2:
 $$ Proof:(p→r)∧(q→r ) \;≡\; (p∨q)→r $$
$$(p→r)∧(q→r)\;≡\;(¬p∨r)∧(¬q∨r)\;≡\; r∨(¬p∧¬q)\;≡\;r∨¬(p∨q) \;≡\;¬(p∨q)∨r\;≡\;(p∧q)→r$$

### Propositional function
>A **propositional function** is a statement that contains a **variable** and becomes a **proposition** (true or false) once the variable is assigned a specific value.

$P(x)$: x is greater than 3 
- x is the *subject* of the statement
- `is greater than 3` is the predicate: a property that subject has to satisfy

#### Example:
Q(x, y): x + y = 3
- Q(6, 3) → False
- Q(2, 1) → True

## Assert statements
>An **assert statement** is used to test assumptions in a program. If the assertion is **true**, the program continues execution. If it is **false**, the program raises an error, helping to catch bugs early.

#### Example:
assert( 2 > 1 ) True
assert( 0 > 1 ) False

- predicates are greater to establish the correctness of computer programs