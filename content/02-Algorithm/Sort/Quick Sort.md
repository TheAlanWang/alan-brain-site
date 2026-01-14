---
tags:
  - algorithm/sort
---

Quick sort is a [[00-Sorting Overview#Stable vs Unstable|unstable]] sorting algorithm. 
### Key idea
- Pick a pivot and partition the array. 
- Every element smaller than the pivot goes to the left, and every elements greater than or equal to it goes to the right.
- The pivot **ends up** in its correct position,
- Recursively apply the same process until each subarray has at most one element.
### Complexity
- Time Complexity: average O($nlogn$)
	- worst O($n^2$) by splitting into unbalanced halves
- Space Complexity: O($logn$) partition
### Code
in place sort
```python
def quicksort(arr, low=0, high=None):
    if low is None:
        low = 0
    if high is None:
        high = len(arr) - 1

	if high - low <= 0: # if only one elements
	    return

    pivot = arr[high]
    
    i = low # track the position where the next smaller-than-pivot element should go
    for j in range(low, high):
        if arr[j] < pivot:
            arr[i], arr[j] = arr[j], arr[i]
            i += 1

    arr[i], arr[high] = arr[high], arr[i] #swap pivot to i-th

    quicksort(arr, low, i - 1)
    quicksort(arr, i + 1, high)
    
    return arr
```

### Hint: make it more memorable
- Maintain invariant: `arr[low:i] < pivot`
- Always use `low` and `high` as the **boundaries**