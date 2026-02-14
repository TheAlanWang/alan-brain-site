**Durability** (in Kafka) means:
- After the producer receives a “write successful” acknowledgement, the record will still be there—even if brokers crash, the leader changes, or the cluster restarts.**  
- In other words, the write is **safely persisted and replicated**, so it won’t “disappear” after failures.