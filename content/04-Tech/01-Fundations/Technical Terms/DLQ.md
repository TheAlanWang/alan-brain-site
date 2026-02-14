DLQ = Dead Letter Queue

A DLQ is typically implemented as a separate Kafka topic, not a database. We publish failed messages (with error metadata) to the DLQ topic for later inspection and replay, and optionally persist them to a DB for search/auditing.