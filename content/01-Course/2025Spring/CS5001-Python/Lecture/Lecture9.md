---
date: 2025-04-02
tags:
  - neu/CS5001
weight: 9
description: tree, O notation, bfs
link:
  - "[[CS5001Lab11.pdf]]"
mainpage:
  - "[[DV_Course]]"
---
## Tree
```python
class Tree:
    def __init__(self, data, left=None, right=None):
        self.data = data
        self.left = left
        self.right = right

def sum_tree(tree) -> int:
    if tree is None:
        return 0
    return tree.data + sum_tree(tree.left) + sum_tree(tree.right)

def sum_tree2(tree) -> int:
    if tree is None:
        return 0
    result = tree.data
    if tree.left is not None:
        result += sum_tree2(tree.left)
    if tree.right is not None:
        result +=sum_tree2(tree.right)
    return result

def make_full_binary_tree(levels: int, data: int = 0) -> Tree:
    if levels == 0:
        return None
    result = Tree(data, 
                make_full_binary_tree(levels - 1, data * 2 + 1),
                make_full_binary_tree(levels - 1, data * 2 + 2))
    return result

from time import time, sleep

def main():
    # tree = Tree(3, Tree(4, Tree(1), Tree(3)), Tree(2, Tree(3), None))
    # print(sum_tree(tree))
    # print(sum_tree2(tree))
    # print(sum_tree(make_full_binary_tree(4)))
    # print(sum(range(15)))

    tree = make_full_binary_tree(23)

    start_time = time()
    # sleep(1)
    print(sum_tree(tree))
    end_time = time()
    print("sum tree1:", end_time - start_time)

    start_time = time()
    # sleep(1)
    print(sum_tree2(tree))
    end_time = time()
    print("sum tree2:",end_time - start_time)

if __name__ == "__main__":
    main()
```

### Build a full binary tree
```python
def make_full_binary_tree(levels: int, data: int = 0) -> Tree:
    if levels == 0:
        return None
    result = Tree(data, 
                make_full_binary_tree(levels - 1, data * 2 + 1),
                make_full_binary_tree(levels - 1, data * 2 + 2))
    return result
```


> [!note] **Asymptotic computational complexity bound** describes how the **running time or space requirement** of an algorithm grows **as the input size goes to infinity**. It’s a way to talk about the **efficiency** of an algorithm, especially for large inputs.

| Notation      | Meaning     | Description                                                        |
|---------------|-------------|--------------------------------------------------------------------|
| Big O (O)     | Upper bound | The worst-case scenario. The algorithm will run at most this fast. |
| Big Omega (Ω) | Lower bound | The best-case scenario. The algorithm runs at least this fast.     |
| Big Theta (Θ) | Tight bound | The exact growth rate, when upper and lower bounds are the same.   |

### Big-O Notation
>[!note] $f(n)=O(g(n))$ if there exist positive constants $c$ and ${n_0}$ such that $f(n) ≤ c * g(n)$ for all  ${n} ≥ {n_0}$ 

Constant Time:

Pointer time: $O(n^2)$

Q: What the time complexity of the recursive Fibonacci algorithm?
	Exponential growth $O(2^n)$

Q: What is the Evaludation_rpn O notation?
	Time Complexity: $O(n)$
	Space Complexity: $O(n)$

Q: What is fib_iter(n) is **linear time** $O(n)$?
```python
	def fib_iter(n):
    if n <= 1:
        return n
    
    a, b = 0, 1
    for _ in range(2, n + 1):
        a, b = b, a + b
    return b	
```

Q: What is hanoi tower is $O(2^n)$?
- Move `n-1` disks from **Source** → **Auxiliary**
- Move the largest (bottom) disk from **Source** → **Destination**
- Move the `n-1` disks from **Auxiliary** → **Destination**

Q: What is the time complexity of drawing a tree?
- if binary tree is $O(nlogn)$
- if with the one line, the worst case is $O(n^2)$

>[!note] **Amortized constant time**
>Each operation might not always be constant time,  but over a series of many operations, the average per operation is constant.

Hashmap:
- Insert: O(1)
- Update: O(1) 
- Lookup: O(1) 
- Delete: O(1) 

Queue:
- enqueue(): O(1)
- dequeue(): O(1)
- is_empty(): O(1)
- peek(): O(1)
- count(): O(1)

