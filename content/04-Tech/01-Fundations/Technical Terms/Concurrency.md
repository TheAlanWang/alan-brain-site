- Concurrency Definition:
	- means multiple users or requests operating on the system at the **same time**
	- If not handled properly, it can cause issues like duplicate records or data being overwritten.

### Concurrency Errors & How to Solve (Clear Notes)
#### 1) Duplicate Creation
What: Same record created twice.  
Why: check-then-insert, retries/double-click/timeouts.  
Fix:
- DB unique constraint (must-have)
- **Idempotency key** for create APIs (same key → return same result)
- Upsert / insert-on-conflict (atomic “create if not exists”)

#### 2) Lost Update
What: One update overwrites another.  
Why: read → modify → write on the same old version; overwriting full row/blob.  
Fix:
- **Optimistic locking** with `version`  
    `UPDATE ... SET ..., version=version+1 WHERE id=? AND version=?`  
    rows=0 → **409 Conflict**    
- Patch/partial updates (update only changed fields)
- Pessimistic lock (`SELECT ... FOR UPDATE`) when necessary
#### 3) Invalid State Transition
What: Status changes in an invalid/conflicting way.  
Why: not checking current state; concurrent conflicting actions.  
Fix:

- Guarded update (state in WHERE clause)  
    `UPDATE ... SET status='APPROVED' WHERE id=? AND status='SUBMITTED'`  
    rows=0 → 409/422
- State machine rules in code (allowed transitions map)
- **State + version** for stronger safety  
    `WHERE id=? AND status=? AND version=?`