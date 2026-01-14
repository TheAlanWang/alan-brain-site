### Stable vs Unstable
- Stable: If two elements have the **same key**, their **relative order stays the same** after sorting.
- Unstable: If two elements have the **same key**, their **relative order may change** after sorting.
#### Sorting Stability:
- Stable sort
	- Insertion Sort - O($n^2$)
	- [[Bubble Sort]] - O($n^2$)
	- [[Merge Sort]] - O($nlogn$)

- Unstable sort
	- Selection Sort  - O($n^2$)
	- [[Quick Sort]] - O($nlogn$)
	- Heap Sort - O($nlogn$)

#### Example:
- Before: `[2a, 1, 2b]`
- After: unstable is `[1, 2b, 2a]`, stable is `[1, 2a, 2b]`
