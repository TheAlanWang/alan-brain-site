---
date: 2025-01-29
tags:
  - "#neu/CS5001"
pageorder: 4
link:
  - "[[CS5001Slides4.pdf]]"
  - "[[CS5001Lab4.pdf]]"
description: recursion
---
## Mathematical Induction:
- a way to prove that some solution is sound
## Recursion (Botton-up):
1. base case (is the simplest version of the problem and is used to control when the recursion stops.)
2. Inductive step
3. Verify whether steps will lead to base case

Recursive call, the program is taking a *leap of faith* that if the smaller problem can be solved, then the large problem will also be solved.

### Cons and pros
#### cons
1. Simpler and Concise Code
2. Recursion is a good option for implementing mathematical definitions concisely.
3. Eliminates the Need for Complex Loops
#### pros
1. Memory Consumption (Stack Overflow)
	- stack growth (tail recursion optimization is a technique used to automatically address the issue of growth in the execution stack - python not support)
2. Performance Overhead:
	- Fibonacci numbers.
3. Difficult to debug:
4. Not Always Intuitive
	- recursion can sometimes be hard to wrap your head around.

### Recursion Debug
- printout: Start → Start function → End function → End
```python
def factorial(n, level):
    print(f"{'>' * level} factorial({n}):start")
    if n == 1:
        product = 1
    else:
        product = n * factorial(n-1, level + 1)

    print(f"{'>' * level} factorial({n}):end return {product}")
    return product
# output
# > factorial(3):start
# >> factorial(2):start
# >>> factorial(1):start
# >>> factorial(1):end return 1
# >> factorial(2):end return 2
# > factorial(3):end return 6
```

## Break big problem to small:
each step to solve the small problem
 
```python
### Solving Self-Similar Problems
def solve_problem(problem) :
	# Reduce to smaller problem
	chunk, smaller_problem = reduce_problem(problem)
	# Solve smaller problem
	smaller_solution = solve_problem(smaller_problem)
	# Combine with rest of problem
	solution = combine (chunk, smaller_solution)
	return solution
```
#### Factorial:
$$ n! = n \times (n-1) \times (n-2) \times \dots \times 1 $$
```python
def factorial(n):
	if n == 0: # Base case
		return 1
	else:
		return n * factorial(n - 1) # Recursive call
print(factorial(5)) # Output: 120
```

otherwise overflow

#### Fibonacci Numbers

$$ F(n) = \begin{cases} n, & \text{if } n ≤ 1 
	\\ F_{n-1} + F_{n-2}, & \text{if } n \geq 2 \end{cases} $$


f_0 = 0
f_1 = 1
f_2 = 1
f_3 = 2
f_4 = 3
f_5 = 5
f_6 = 8

```python
def fib(n):
	if n <= 1:
		return n
	else:
		return fib(n-1)+fib(n-2)

for n in range(30):
	print(fib(n))
```
## Trees

![[Pasted image 20250129153620.png|450]]

subtrees are also just trees
###### Key Concepts of Trees
- **Root Node** – The topmost node in a tree.
- **Parent & Child Nodes** – A node connected directly below another node is called a **child**, and the above node is its parent.
- **Siblings** – Nodes that share the same parent.
- **Leaf Nodes** – Nodes that do not have any children.
- **Depth** – The number of edges from the root to a node.
- **Height** – The number of edges on the longest path from the root to a leaf.
- **Subtree** – A tree formed by any node and its descendants.

```python
# Define constants for data positions
Data = 0
LEFT = 1
RIGHT = 2

# Function to create a node
def make_node(data):
	return [data, None, None]

def print_tree_post_order(tree):
	if tree[LEFT]:
		print_tree_post_order(tree[LEFT])
	if tree[RIGHT]:
		print_tree_post_order(tree[RIGHT])
	print(tree[Data], end=" ")

# Pre-order traversal: Root, Left, Right
def print_tree_pre_order(tree):
	print(tree[Data], end=" ") # Print the root
if tree[LEFT]:
	print_tree_pre_order(tree[LEFT]) # Recurse left
if tree[RIGHT]:
	print_tree_pre_order(tree[RIGHT]) # Recurse right

# In-order traversal: Left, Root, Right
def print_tree_in_order(tree):
	if tree[LEFT]:
		print("(", end = " ")
		print_tree_in_order(tree[LEFT]) # Recurse left
		print(tree[Data], end=" ") # Print the root
	if tree[RIGHT]:
		print_tree_in_order(tree[RIGHT]) # Recurse right
		print(")", end = " ")

def main():
	# Root operator "/"
	expr_root = make_node('/') # ['/', None, None]
	# print("expr_root=" , expr_root)
	# Left child is multiplication "*"
	prod = make_node('*')
	expr_root[LEFT] = prod # expr_root= ['/', ['*', None, None], None]
	# print("expr_root=" , expr_root)
	# Right child is subtraction "-"
	diff = make_node('-')
	expr_root[RIGHT] = diff
	# print("expr_root=" , expr_root) # expr_root= ['/', ['*', None, None], ['-', None, None]]

	# Multiplication children (3 and "-")
	prod[LEFT] = make_node(3)
	diff2 = make_node('-') # diff2 = ['-', None, None]
	# print("diff2= ", diff2)
	
	prod[RIGHT] = diff2
	diff2[LEFT] = make_node(4)
	diff2[RIGHT] = make_node(1)

	# Subtraction children (6 and 3)
	diff[LEFT] = make_node(6)
	diff[RIGHT] = make_node(3)
	print("expr_root=" , expr_root)

# ['/',
# ['*',
# [3, None, None],
# ['-',
# [4, None, None],
# [1, None, None]
# ]],
# ['-',
# [6, None, None],
# [3, None, None]]]

	# Print different tree traversals
	print("Pre-order expression: ", end="")
	print_tree_pre_order(expr_root)
	print()
	print("In-order expression: ", end="")
	print_tree_in_order(expr_root)
	print()
	print("Post-order expression: ", end="")
	print_tree_post_order(expr_root)
	print()

# Main function call
if __name__ == "__main__":
	main()
```

### Tree traversal
```python
'''
	 A 
	/ \ 
   B   C 
  / \ 
 D   E
'''
```
Pre-order traversal would be: `A B D E C`
In-order traversal would be: `D B E A C`
Post-order traversal would be: `D E B C A`


## Tower of Hanoi
```python
num_step = 0

def tower_of_hanoi(num_disks, source_rod, dest_rod, spare_rod):
	global num_step
	num_step += 1
	# Base case: if there is only one disk, move it directly from source to destination
	if num_disks == 1:
		print(f"Move disk 1 from {source_rod} to {dest_rod}")
		# return
	else:
		print("num_disks", num_disks)
	# Move num_disks-1 disks from source to spare_rod, using destination as spare_rod
	tower_of_hanoi(num_disks-1, source_rod, spare_rod, dest_rod) # source to spare_rod
	# Move the nth disk from source to destination
	print(f"Move disk {num_disks} from {source_rod} to {dest_rod}")
	# Move num_disks-1 disks from spare_rod to destination rod, using source as spare_rod
	tower_of_hanoi(num_disks-1, spare_rod, dest_rod, source_rod)
	
# Main function to run the Tower of Hanoi problem
def main():
	num_disks = 3 # Number of disks
	tower_of_hanoi(num_disks, '1', '2', '3') # A = source_rod, B = dest_rod, C = spare_rod
# print(num_step)
if __name__ == "__main__":
	main()
```

## Fibonacci Number
```python
def fib_iter(n):
	if n == 0:
	return 0
	
	prev = 0
	cur = 1
	for i in range(n-1): # Start from 1 because we've already handled n = 0 case
		next_fib = prev + cur
		prev = cur
		cur = next_fib
	return cur

for n in range(10):
	print(fib_iter(n))
```
