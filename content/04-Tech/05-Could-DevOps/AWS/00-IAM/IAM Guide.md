1) List all local AWS profiles (entry points to your credential list)
```bash
aws configure list-profiles
```

2) Remove a specific profile (manually delete its section)

```bash
nano ~/.aws/credentials
```

3) Use a specific profile in `boto3`
```python
import boto3

sess = boto3.Session(profile_name="myuser", region_name="us-east-1")
print(sess.client("sts").get_caller_identity())
```

