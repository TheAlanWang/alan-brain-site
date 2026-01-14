---
date: 2025-01-15
tags:
  - neu/CS5001
pageorder: 2
link:
  - "[[CS5001Slides2.pdf]]"
  - "[[CS5001Lab2.pdf]]"
description: data type, condition, iterative
---

`//` floor division
### Turing Machine
Turing Completeness:
1. conditional logic
2. looping or recursion

### Turing Completeness
- **Conditionals (if-then statements)** - Making decisions based on certain conditions.
- **Loops or iteration** - Repeating actions based on certain conditions.
- **Memory manipulation** - Storing and accessing data.

*halt*

Church-Turing Thesis:
- Anything that can be in principle computed, can be computed by a Turing machine.

### Boolean
True, False
##### Relational Operators
`<` less than
`>`
`<=`
`>=`
`==`
`!=`
Notes: `≥` and `≤`syntax error

**Notes**: These have ==lower== precedence than the *arithmetic operators*(`+` `-` `*` `/` `//` `
%`)
**Also Notes:** These ?????

```python
"cat" < "dog" # Alphabet
```


True = 1; False = 0

### String comparison:
Step-by-step
```python
if "Zoo" < "aardvark":
	print("Z < a, Z is samll")
```
1. **First Character Comparison:**
    - `"Zoo"` starts with **'Z'** (ASCII value 90)
    - `"asdsadsa"` starts with **'a'** (ASCII value 97)
2. Since **'Z'** has a samller ASCII value than **'a'**, the comparison immediately determines that **"Zoo" < "aardvark"**.
ABC....Z >abc...z

### Conditional Statements
```python
if condition:
	# block of statements
	# that are only executed if
	# condition is True
else:
	# block of statements
	# that are only executed if
	# condition is False 
# Statments that are 
# executed regardless
```


### Chaining Conditionals
```python
if :
	days = 31
if :
	days = 28
# etc...
```

#### elif

### Compound Boolean Expressions
and, or, not

### DeMorgan's Laws
not (A or B) == (not A) and (not B)
not (A and B) == (not A) or (not B)


### While

```python
while condition:
	# Body of statements that are 
	# executed while condition is True
```

notes: press ctrl+C to send an interrupt signal to Python to forcibly break out the loop

```python
for var in collection:
	# block of statements
	# that will execute
	# once for each item in collection

for i in range(10):
	print(i)
	# block of statements
	# that will execute
	# once for each item in collection

for i in range(3, 9, 3):
	print(i)

for i in range(10, 0, -1):
	print(i)
```

## store collections of items
### Tuples
- Immutability: Only can be read. Can reassigned to point
- String and tuples are **immutable**
```python
tuple_name = (item1, item2, item3, ...)
```

### Lists
- Mutability
- Lists are collections of arbitrary things
```python
my_list = [69, 1.41, "Orange"]

# mutable
my_list.append(1, 2, 3)
my_list[2] = "banana"
del my_list[1]

print(my_list) #Output: [69, 'banana', (1, 2, 3)]
```

#### address
```python
# copy the address
list_2 = list_1

# make new copy, instead of coping the address
list_3 = list_1[:]
```

### Cellular Automata
- A regular grid of cells
- Each cell can be in one of finitely many states
Conway's Game of life

#### Elementary Cellular Automata
Rule 110:

- one dimensional
 Generation 0:
	 `0 1 0`
 Generation 1:
 - There are 8 possible ways
-  Rule set:
	`111 110 101 100 011 010 001 000`
	 `0   1   1   0   1   1   1   0`

```python
# 111 110 101 100 011 010 001 000
#  0   1   1   0   1   1   1   0
#### Elementary Cellular Automata
N = 64
state = [0] * N + [1] + [0] * N

for i in range(N):
# Print the current state
	for cell in state:
		if cell == 1:
		print('#', end = '')
	else:
		print(' ', end = '')
	print()
	## evolve the state according to the rules:
	next_state = []
	for j in range(len(state)):
		# Determine the neighborhood
		if j - 1 >= 0:
		pre = state[j - 1]
		else:
		pre = 0
		cur = state[j]
		if j + 1 < len(state):
			next = state[j + 1]
		else:
			next = 0
		neighborhood = (pre, cur, next)
		if neighborhood in [(1, 1, 1), (1, 0, 0), (0, 0, 0)]:
			next_state.append(0)
		else:
		next_state.append(1)
		# Update the state for the next generation
	state = next_state 
```