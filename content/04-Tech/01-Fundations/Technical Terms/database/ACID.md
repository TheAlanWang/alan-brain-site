4 guarantees that make a database transaction reliable
- A: Atomicity
	- A transaction is “all or nothing.”
- C: Consistency
	- Rules/constraints stay valid before and after the transaction.
- I: Isolation
	- Concurrent transactions don’t interfere; no one sees half-finished work.
- D: Durability
	- Once committed, data won’t be lost even after crashes/power failure.