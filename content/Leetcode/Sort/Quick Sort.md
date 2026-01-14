```python
def quicksort(arr, low=None, high=None):
    if low is None:
        low = 0
    if high is None:
        high = len(arr) - 1

	if high - low <= 1:
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
```