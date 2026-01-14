Merge sort
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
            
            if l < len(left):
                res.extend(left[l:])
                
            elif r < len(right):
                res.extend(right[r:])
            
            return res

        def mergesort(nums):
            if len(nums) == 1:
                return nums

            mid = len(nums) // 2

            left_part = mergesort(nums[0: mid])
            right_part = mergesort(nums[mid: len(nums)])
            
            return merge(left_part, right_part)

        return mergesort(nums)
```