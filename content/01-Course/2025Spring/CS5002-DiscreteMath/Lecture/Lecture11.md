---
tags:
  - neu/CS5002
description: combination
weight: 11
---
## Review:
### Permutation: 
>A **permutation** is an arrangement of objects **in a specific order**
$$P(n,r)=\frac{n!}{(n-r)!}$$
### Combinations
$$C(n, r)=\frac{n!}{(n-r)!r!}$$
## Lecture
### Question 1 how to coding Combination
#### 1.1 coding factorial
```python
def factorial(N):
'''
Arg: N (int) - representing the number for which factorial is calculated. 
Return: int - N! = N * (N-1) * (N-2) * ... * 1
'''
	result = 1 
	for i in range(2, N + 1): # Loop from 2 to N (1 is unnecessary) 
		result *= i 
		return result
```

- think??? why range from 2 to N + 1
- Cause when i = 0 or 1, the answer will be the same `1`
#### 1.2 coding permutation(by using factorial function)
```python
def permutation_iterative(n: int, r: int) -> int: 
""" 
Compute P(n, k) using an iterative approach. 
""" 
	numerator = factorial(n)
	denominator = factorial(n - r)
	return numerator/denominator
```
#### 1.3 coding combination - using recursion
##### Pascal's identity-Combinatorial Proof
$$\binom{n}{k}=\binom{n-1}{k-1}+\binom{n-1}{k}$$
> To prove the Pascal's identity:

$\binom{n}{k}$ represents the number of ways to choose `k` elements from a set of `n` distinct elements.
$\binom{n-1}{k-1}$ represent put 4 in the first spot
$\binom{n-1}{k}$ represent not put 4 in the first spot
- Since every subset of k elements must either include x or exclude it, the total number of ways to choose k elements from a set of n elements is the sum of these two cases

Base Case:
$$\binom{n}{0}=\frac{n!}{(n-0)!0!}=1$$
$$\binom{n}{n}=\frac{n!}{(n-n)!n!}=1$$

```python
def combination_recursion(n: int, k: int):
''' 
Compute the binomial coefficient C(n, k) using recursion. 
Args: 
	n (int): The total number of elements. 
	k (int): The number of elements to choose. 
	Returns: int: The value of C(n, k) = n! / (k!(n-k)!). 
''' 
	if k == 0 or n == k:
		return 1
	else:
		return combination_recursion(n-1, k-1)+combination_recursion(n-1, k)
```

### Question2 Poker
Royal Flash: (same suit)
- `A` `K` `Q` `J` `10`
Straight Flash: 
- `K` `Q` `J` `10` `9` (same suits)
- `Q` `J` `10` `9` `8` (same suits)
Straight:
- `K` `Q` `J` `10` `9` (different suits)
Flash:
- same suits ( Hearts♥, Diamonds ♦, Clubs ♣, Spades ♠)

###### 2.1 Probability of Royal Flash?
$$P(Royal\;Flash)=\frac{|Royal\;flash|}{|S|}=\frac{4}{\binom{52}{5}}$$
###### 2.2 Probability of Straight Flash?
$$P(Straight\;Flash)=\frac{|Straight\;flash|}{|S|}=\frac{4*9}{\binom{52}{5}}=\frac{36}{\binom{52}{5}}$$
Although there may 10,
1~5, 2~6, 3~7, 4~8, 5~9, 6~10, 7~11, 8~12, 9~13
(exclude 10~1, this is Royal Flash)
###### 2.3 Probability of Straight?
$$P(Straight)=\frac{|Straight|}{|S|}=\frac{\binom{4}{1}^5*10-4-36}{\binom{52}{5}}$$
###### 2.4 Probability of Flash?
$$P(Flash)=\frac{|Flash|}{|S|}=\frac{\binom{13}{5}*4-4-36}{\binom{52}{5}}$$
### Question3 Die
>Roll a die and I know that the number rolled is even. What's the probability of roll of a 2?

S = {1, 2, 3, 4, 5, 6}
S = {2, 4, 6} -> the given information modified my sample space.

Like machine learning base on **conditional probability**.
#### Conditional Probability
$$P(A|B)=\frac{P(A\cap B)}{P(B)}$$

Rolling die:
$$P(roll\;a\;2 | number\;rolled\;is\;even) = \frac{P(roll\;2\;even)}{P(even)}=\frac{1/6}{1/2}=\frac{1}{3}$$

### Question4 Two people Chocolate
We have two people, One of two people like chocolate, what's the probability that the other person like chocolate?
- $CC$ 
- $\bar{C}\bar{C}$ (exclusive)
- $\bar{C}C$  
- $C\bar{C}$
$$Probability =\frac{1}{3}$$

If the question become to: The first person like chocolate, $\frac{1}{2}$will be the answer.