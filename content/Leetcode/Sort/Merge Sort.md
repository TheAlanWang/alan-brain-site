---
tags:
  - algorithm/sort
---
Merge sort is a [[00-Sorting Overview#Stable vs Unstable|stable]] sorting algorithm. We pick from the left half first when values are equal.
### Key idea
Divide & Conquer
- Divide step: 
	- **Recursively** split the array into halves until each subarray has at most one element.
- Conquer (Merge step): 
	- Merge two sorted halves using two pointers. Repeatedly append the smaller element to the result. 
	- When one pointer reaches the end, append the remaining elements from the other half.
### Complexity
- Time Complexity: O($nlogn$) in all cases
- Space Complexity: $O(n)$ 
	- O(n) for merge function
	- $O(nlogn)$ for recursion stack
### Code
```python
class Solution:
    def sortArray(self, nums: List[int]) -> List[int]:
        def merge(left: List[int], right: List[int]):
            res = []
            l = r = 0
            
            while l < len(left) and r < len(right):
                if left[l] <= right[r]:
                    res.append(left[l])
                    l += 1
                else:
                    res.append(right[r])
                    r += 1
            
            res.extend(left[l:])    
            res.extend(right[r:])
            return res

        def mergesort(nums):
            if len(nums) <= 1:
                return nums

            mid = len(nums) // 2
            left_part = mergesort(nums[0: mid])
            right_part = mergesort(nums[mid: len(nums)])
            
            return merge(left_part, right_part)

        return mergesort(nums)
```

### Hint to write merge sort:
- Two Function:
	- Function`def merge(left_arr, right_arr)` 
	- Function `def mergesort(array)`: recursively split and sort
- Function`def merge(left_arr, right_arr)` 
	- two pointers
	- while both valid: append smaller
	- `extend` the remaining
- Function: `mergesort(array)`
	- base case -> split -> recursively sort -> combine
		- recursively sort: `sorted_left = mergesort(left)`, `sorted_right = mergesort(right)`