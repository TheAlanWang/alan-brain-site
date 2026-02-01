### Basic JSON rules
Keys must use double quotes
✅ `"name"`  ❌ `'name'`
    
String values must also use double quotes
✅ `"Alan"`      ❌ `'Alan'`
    
Supported data types
    - string: `"hi"`
    - number: `123`, `3.14`
    - **boolean**: `true`, `false` _(lowercase — not `True/False`)_
    - null: `null` _(not `None`)_
    - object: `{ ... }`
    - array: `[ ... ]`
        
- No comments allowed
- No trailing commas (no extra comma at the end)  
    ✅ `{"a": 1}`  
    ❌ `{"a": 1,}`
### json.dumps()
json.dumps turns a Python dictionary into a JSON string. 
- With a dictionary, you can use f-strings for dynamic values, then pass the result to APIs that require a JSON string.

```python
import json

# 用 f-string 拼变量
account_id = "123456789012"
user_name = "assignment1_User"

# Python dictionary
policy = {
    "Version": "2012-10-17",
    "Statement": [{
        "Effect": "Allow",
        "Principal": {"AWS": f"arn:aws:iam::{account_id}:root"},
        "Action": "sts:AssumeRole",
        "Resource": f"arn:aws:iam::{account_id}:user/{user_name}",
    }],
}

# dict → JSON 字符串
trust = json.dumps(policy)

print(trust)
# {"Version": "2012-10-17", "Statement": [{"Effect": "Allow", "Principal": {"AWS": "arn:aws:iam::123456789012:root"}, "Action": "sts:AssumeRole", "Resource": "arn:aws:iam::123456789012:user/assignment1_User"}]}
```