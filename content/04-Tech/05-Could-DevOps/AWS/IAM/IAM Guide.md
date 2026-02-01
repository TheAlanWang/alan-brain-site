
列出你本机所有 profiles（= credential 列表入口)
```bash
aws configure list-profiles
```


2) 删除某个 profile（手动删配置段）

```bash
nano ~/.aws/credentials
```


boto3 用这个 profile
```python
import boto3

sess = boto3.Session(profile_name="myuser", region_name="us-east-1")
print(sess.client("sts").get_caller_identity())
```

