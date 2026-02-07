---
date: 2026-02-06
---
## Review
### AWS Lambda
lambda only run 3 second
- Backend logic for APIs  
    You can connect Lambda to an HTTP endpoint (usually via API Gateway). When a request comes in, Lambda runs your handler and returns a response.
- Event-driven automation  
    Example: when a file is uploaded to an S3 bucket, S3 can trigger a Lambda function to process it (resize images, parse PDFs, generate thumbnails, etc.).
- Processing queues and streams  
    For SQS, Kinesis, DynamoDB Streams, etc., Lambda can consume records via an event source mapping. Some sources “push” events, and for queues/streams Lambda typically “pulls” via the mapping.
- Scheduled jobs (cron-style)  
    You can run Lambda on a schedule using EventBridge rules (for example, every hour or every day).

**cloud native application**

**Microservice Architecture**
- Asynchronous
	- SQS or SNS
- Synchronous

---
## Lecture
Database vs file systems

Structured database vs no Structured database

creating an index can accelerate searching speed
```sql
CREATE INDEX index_name ON table1 (Name);
```

But if duplicates already exist, it fails If `users.email` already has two rows with the same email, the database will reject the UNIQUE index

Join

### Relational vs NoSQL database
- NoSQL database called key-value stores
- AWS key-values store, DynamoDB
- AWS Relational Database
	- AWS RDS: Relational Database Servie
	- AWS Aurora: Database
### DynamoDB
- Serverless: means you build and run applications without managing servers
- Distributed: auto scale

- DynamoDB Component
	- DAX clusters: Caching
	- create Table
		- need a primary key, allow 1 or 2 attribute (column is attributes)
			- Partition key(hash key)
			- Sort key(range key)
		- schema
- Index  
	- GSI (Global Secondary Index)
- DynamoDB - Explore items
	- Scan (low efficiency)
		- Scan whole table
		- Filter
	- Query (high efficiency)
		- Must base on the partition key
		- binary search tree


The table’s partition key supports only one query access pattern. If you want to query using a different key, you need a GSI (Global Secondary Index).

- chose userid, game id
	- Partition key: choose more different value
