---
tags:
  - algorithm/sort
---
### Key idea
Bubble sort is a [[00-Sorting Overview#Stable vs Unstable|stable]] sorting algorithm. 
- It repeatedly sweeps through the list, comparing and swapping **adjacent** elements if needed. 
- After each full pass, the **largest element** will be at the end, so the next pass only needs to consider one **fewer** element. 

### Complexity
- Time Complexity: O($n^2$) in average/worst case (nested loops).
- Space Complexity: O(1) in place

### Code
```python
n = len(nums)
for i in range(n - 1):
	for j in range(n - 1 - i):
		if nums[j] > nums[j + 1]:
			nums[j], nums[j + 1] = nums[j + 1], nums[j]

return nums
```

