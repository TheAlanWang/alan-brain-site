DynamoDB is a fully managed, serverless NoSQL database on AWS that provides fast, scalable key-value and document storage with low-latency reads and writes.

### SQL vs Non SQL
- SQL
	- Supports `JOIN`, strong consistency/transactions [[ACID]]
	- Example: mysql, PostgreSQL
- Non SQL
	- Flexible schema + high-scale
	- Example: `Redis`
### Table Name constraints
- PascalCase (`S3ObjectSizeHistory`)
- kebab-case (`s3-object-size-history`)
- not allow `/` and space ` `
- same account + same region: table name must be unique (No global uniqueness requirement)

