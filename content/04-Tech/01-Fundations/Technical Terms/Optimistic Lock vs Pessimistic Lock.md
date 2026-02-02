### Preventing **Lost Updates** with Database Locking
When multiple requests update the **same record** at the same time, you can get **lost updates** (one change overwrites another).

## Optimistic Lock
- Assume conflicts are **rare**.  
- No lock on read. 
- On write, **verify** nobody changed the row since you read it.

## Pessimistic Lock
- Assume conflicts are **common**.  
- Lock the row immediately so others must wait.

### Purpose
1) Prevent “lost updates” (overwrite writes).
2) Consistency / Correctness