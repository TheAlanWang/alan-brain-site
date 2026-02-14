## Trusted relationships (IAM role)
A role’s trusted relationships come from its trust policy (AssumeRolePolicyDocument). This policy says who is allowed to assume the role.

|Term|Meaning|
|---|---|
|Trusted relationships|Who is trusted to assume this role (defined in the trust policy)|
|Trusted entities|The principals listed in the trust policy (who is allowed)|
|In your case|Any principal in your account can assume this role (if they also have the sts:AssumeRole permission on their identity|

```python
import json

def create_iam_roles(account_id):
    trust = json.dumps({
        "Version": "2012-10-17",
        "Statement": [{
            "Effect": "Allow",
            "Principal": {"AWS": f"arn:aws:iam::{account_id}:root"},
            "Action": "sts:AssumeRole",
        }],
    })
    iam.create_role(RoleName="Dev", AssumeRolePolicyDocument=trust)
    iam.create_role(RoleName="User", AssumeRolePolicyDocument=trust)
```

| 情况                                     | 会发生什么                                 |
| -------------------------------------- | ------------------------------------- |
| 只配 Trust policy（Role 上），不配 User policy | 用户没有 sts:AssumeRole 权限 → AccessDenied |
| 只配 User policy（User 上），不配 Trust policy | Role 不信任该用户 → AccessDenied            |
| 两边都配                                   | 可以成功 assume                           |