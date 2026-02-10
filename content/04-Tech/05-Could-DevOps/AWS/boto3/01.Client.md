
```python
import boto3

# AWS clients (uses default credentials from ~/.aws/credentials)
iam = boto3.client("iam")
sts = boto3.client("sts")   # Security Token Service: AssumeRole (assume role to get temporary credentials)
s3 = boto3.client("s3", region_name=REGION)
```

- `iam = boto3.client("iam")`
	- Means: `boto3.client("s3")` creates an object that lets you use Python to call S3’s remote API.
		- object: An object is used to bundle a set of related capabilities (methods) and state (data/configuration) together. methods: 
			- method: `list_buckets()`
			- config: region

```python
iam.create_role()
iam.attach_role_policy()
iam.create_access_key()

sts.get_caller_identity()["Account"]

s3_client = sess.client("s3")
```