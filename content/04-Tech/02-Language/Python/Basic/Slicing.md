# Example
Let `k` be the count of elements you want (typically `0 <= k <= len(arr)`).
Take k elements:
```python
arr[:k]
```

Take last k elements:
```python
arr[-k:]
```

Take another elements, without last k
```python
arr[:-k]
```

## Split at `i` and recombine exactly:
```python
arr[:i] + arr[i:] == arr
```
### Empty slice is natural
```python
arr[i:i] == []
```

`rotated = arr[k:] + arr[:k]`

## Slice
```python
arr = [1, 2, 3, 4, 5]
left = 2
right = 5
length = len(arr[left: right])
```
Because right is excluded. length = right - left = 3

---
# Slicing

## 1) What is slicing?

**Slicing** extracts a **subsequence** from a sequence (list, string, tuple, etc.) using:

```py
seq[start:stop:step]
```

- `start` is **inclusive**
- `stop` is **exclusive**
- `step` defaults to `1`

---

## 2) Basic forms (memorize)

Assume:
```py
a = [0,1,2,3,4,5]
```

### Prefix / suffix
```py
a[:3]    # [0,1,2]
a[3:]    # [3,4,5]
a[:]     # shallow copy of the whole list
```

### Middle range
```py
a[1:4]   # [1,2,3]
```

---

## 3) Negative indices (from the end)

```py
a[-1]    # 5 (last element)
a[-2:]   # [4,5]
a[:-1]   # [0,1,2,3,4]  (drop last)
a[1:-1]  # [1,2,3,4]    (drop first & last)
```

---

## 4) Step (every k elements)

```py
a[::2]   # [0,2,4]
a[1::2]  # [1,3,5]
a[2:6:2] # [2,4]
```

---

## 5) Reverse with slicing

```py
a[::-1]  # [5,4,3,2,1,0]
```

**Important:** `a[::-1]` returns a **new list** (copy).  
In-place reverse (list only):
```py
a.reverse()   # mutates a
```

---

## 6) Slicing works on strings and tuples too

### String
```py
s = "abcdef"
s[1:4]   # "bcd"
s[::-1]  # "fedcba"
```

### Tuple
```py
t = (10,20,30,40)
t[1:]    # (20,30,40)
```

---

## 7) Out-of-range behavior (safe)

Python slicing never throws IndexError due to range overflow:

```py
a[:100]   # returns whole list
a[100:]   # []
a[-100:3] # [0,1,2]
```

---

## 8) Copy vs view (very important!)

### For lists, slicing creates a **new list**
```py
b = a[1:4]
```

### But it is a shallow copy
Nested example:
```py
x = [[1],[2]]
y = x[:]          # shallow copy (outer list is new)
y[0].append(9)
# x is now [[1,9],[2]] because inner lists are shared
```

If you need a deep copy, use:
```py
import copy
z = copy.deepcopy(x)
```

---

## 9) Slice assignment (lists only) — super useful

### Replace a range
```py
a = [0,1,2,3,4]
a[1:4] = [9,9]
# [0,9,9,4]
```
**Length can change** when replacing slices.

### Insert (empty slice)
```py
a = [0,1,2,3]
a[2:2] = [7,8]
# [0,1,7,8,2,3]
```

### Delete (assign empty list)
```py
a = [0,1,2,3,4]
a[1:3] = []
# [0,3,4]
```

### Delete with step (every other element)
```py
a = [0,1,2,3,4,5]
del a[::2]
# [1,3,5]
```

---

## 10) `slice` object (rare, but good to know)

```py
sl = slice(1, 5, 2)
a[sl]      # same as a[1:5:2]
```

---

## 11) Time & space complexity (interview-critical)

Let `k = length of the slice`:

- `a[i:j]` time: **O(k)**
- space: **O(k)** (creates a new list/string/tuple)

Index access `a[i]` is **O(1)**.

### Common pitfall: accidental O(n^2)
Bad:
```py
for i in range(n):
    part = a[:i]  # slicing each time costs O(i)
```

---

## 12) Interview patterns using slicing

### Last k elements
```py
a[-k:]
```

### Drop first / last
```py
a[1:]
a[:-1]
```

### Reverse
```py
a[::-1]
```

### Palindrome check (strings)
```py
s == s[::-1]
```

### Be careful in sliding window problems
Slicing inside a loop can be expensive:
```py
# O(n*k) due to slicing
for i in range(n-k+1):
    window = a[i:i+k]
```
Prefer two pointers / maintain state incrementally.