---
date: 2025-04-09
tags:
  - neu/CS5001
weight: 10
link:
  - "[[CS5001Lab12.pdf]]"
description: Big O notation, linear search
mainpage:
  - "[[DV_Course]]"
---
Big O notation
- The Priority Queue ADT

| Time complexity | Priority Queue | Unsorted list<br>(+cache  lowest priority item) | Ordered list | heap<br>(complete binary tree) |
| --------------- | -------------- | ----------------------------------------------- | ------------ | ------------------------------ |
| insert          | O(1)           | ≤ O(1) am                                       | O(n)         | ==**O(log n)**==               |
| pop_first O(n)  | O(n)           | O(n)                                            | O(1)         | O(log n)                       |
| is_empty        | O(1)           | O(1)                                            | O(1)         | O(1)                           |
| peek            | O(n)           | >O(1)                                           | O(1)         | O(1)                           |
| count           | O(1)           | O(1)                                            | O(1)         | ==O(1)==                       |
[[Heap]]
Tree traversal: Time complexity O(n)

Linear Search: Time complexity O(n)
Binary Search: Time complexity O(logn) ordered data

### Sorting
Selection Sort: Find smallest element put it first. $O(n^2)$
Insertion Sort: Find each element insert it into the initial sort portion in right place. $O(n^2)$
Bubble Sort: The largest number will be first sort. $O(n^2)$
Merge Sort: 

- **Insertion Sort**:  "Insert step by step into the right place, like sorting playing cards in your hand."
- **Selection Sort**:  "Pick the smallest value each time and put it at the front."
- Quick Sort: 