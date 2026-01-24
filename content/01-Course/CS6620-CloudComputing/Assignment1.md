# AWS boto3 IAM + S3 Role-Assumption Assignment Manual

This manual walks you through an end-to-end implementation using **AWS Python SDK (boto3)** to:

1. Create two IAM roles: `Dev` and `User`
2. Attach IAM policies:
   - `Dev`: **full access to S3**
   - `User`: **only list/get S3 buckets and objects**
3. Create an IAM user (name is up to you)
4. Use the created user to assume the `Dev` role and:
   - Create **one S3 bucket** (globally unique name)
   - Upload `assignment1.txt` with content: `Empty Assignment 1`
   - Upload `assignment2.txt` with content: `Empty Assignment 2`
   - Upload `recording1.jpg` (a small image)
5. Quit `Dev`, assume `User`, then:
   - Find all objects with prefix `assignment`
   - Compute **total size** (bytes)
6. Quit `User`, assume `Dev`, then:
   - Delete all objects and delete the bucket

> ✅ The included script completes the full workflow in one run.

---

## 0) Prerequisites (one-time)

### A. AWS Account (Free Tier)
Create an AWS account (free tier is fine). You need *some* admin-level identity to bootstrap IAM resources.

### B. Create an admin profile for bootstrapping (recommended)
In the AWS Console:

1. IAM → Users → Create user (example: `bootstrap-admin`)
2. Attach policy: **AdministratorAccess**
3. Create an **Access Key** for this user (Access key ID + Secret access key)

On your machine:

```bash
pip install boto3 awscli
aws configure --profile admin
# Paste:
# AWS Access Key ID
# AWS Secret Access Key
# Default region name (e.g., us-east-1)
# Default output format (json)
```

---

## 1) Run the assignment script (boto3)

### A. Save this file as `iam_s3_assignment.py`

> This script:
> - Creates the IAM user + roles
> - Adds policies
> - Assumes roles
> - Creates bucket + uploads files
> - Lists `assignment*` objects and sums sizes
> - Deletes objects + bucket

```python
import boto3
import json
import uuid
import base64
from pathlib import Path

# ----------------------------
# Config (edit if you want)
# ----------------------------
ADMIN_PROFILE = "admin"            # your bootstrap admin profile
NEW_IAM_USER_NAME = "assignment-user-" + uuid.uuid4().hex[:8]  # or set a fixed name
ROLE_DEV = "Dev"
ROLE_USER = "User"

DEV_MANAGED_POLICY_ARN = "arn:aws:iam::aws:policy/AmazonS3FullAccess"
USER_INLINE_POLICY_NAME = "UserS3ListGetOnly"
USER_ASSUME_POLICY_NAME = "AllowAssumeDevAndUserRoles"

# A tiny valid 1x1 JPEG (base64). No external libs required.
TINY_JPEG_BASE64 = (
    "/9j/4AAQSkZJRgABAQAAAQABAAD/2wCEAAEBAQEBAQEBAQEBAQEBAQEBAQEBAQEBAQEBAQEB"
    "AQEBAQEBAQEBAQEBAQEBAQEBAQEBAQEBAQEBAQEBAQEBAQEBAQEBAQEBAQEBAQEBAQEBAQH/"
    "2wCEAQEBAQEBAQEBAQEBAQEBAQEBAQEBAQEBAQEBAQEBAQEBAQEBAQEBAQEBAQEBAQEBAQEB"
    "AQEBAQEBAQEBAQEBAQEBAQEBAQEBAQEBAQEBAQEBAQEBAQEBAQH/wAARCAAQABADASIAAhEB"
    "AxEB/8QAFQABAQAAAAAAAAAAAAAAAAAAAAb/xAAgEAACAQQCAwAAAAAAAAAAAAABAgMABAUR"
    "BhIhMUFR/8QAFQEBAQAAAAAAAAAAAAAAAAAAAQT/xAAXEQEAAwAAAAAAAAAAAAAAAAABABES"
    "/9oADAMBAAIRAxEAPwCq2m2Q0y0x3bUuKq7i3QhR0g8cZ7rj1X5kS7cF5a2cQpGQWJ5yT1r"
    "xw9qkq6oWm0p8qkqf/2Q=="
)

# ----------------------------
# Helpers
# ----------------------------
def get_account_id(sts_client):
    return sts_client.get_caller_identity()["Account"]

def ensure_iam_user(iam, username):
    try:
        user = iam.get_user(UserName=username)["User"]
        return user["Arn"]
    except iam.exceptions.NoSuchEntityException:
        user = iam.create_user(UserName=username)["User"]
        return user["Arn"]

def create_access_key(iam, username):
    # IAM users can have max 2 access keys.
    existing = iam.list_access_keys(UserName=username)["AccessKeyMetadata"]
    if len(existing) >= 2:
        raise RuntimeError(
            f"IAM user '{username}' already has 2 access keys. "
            "Delete one and rerun."
        )
    resp = iam.create_access_key(UserName=username)["AccessKey"]
    return resp["AccessKeyId"], resp["SecretAccessKey"]

def ensure_role(iam, role_name, trust_policy):
    try:
        role = iam.get_role(RoleName=role_name)["Role"]
        # Update trust policy to ensure correct principal
        iam.update_assume_role_policy(RoleName=role_name, PolicyDocument=json.dumps(trust_policy))
        return role["Arn"]
    except iam.exceptions.NoSuchEntityException:
        role = iam.create_role(
            RoleName=role_name,
            AssumeRolePolicyDocument=json.dumps(trust_policy),
            Description=f"Role for assignment: {role_name}",
        )["Role"]
        return role["Arn"]

def attach_managed_policy_if_needed(iam, role_name, policy_arn):
    attached = iam.list_attached_role_policies(RoleName=role_name)["AttachedPolicies"]
    if not any(p["PolicyArn"] == policy_arn for p in attached):
        iam.attach_role_policy(RoleName=role_name, PolicyArn=policy_arn)

def put_inline_role_policy(iam, role_name, policy_name, policy_doc):
    iam.put_role_policy(
        RoleName=role_name,
        PolicyName=policy_name,
        PolicyDocument=json.dumps(policy_doc),
    )

def put_inline_user_policy(iam, username, policy_name, policy_doc):
    iam.put_user_policy(
        UserName=username,
        PolicyName=policy_name,
        PolicyDocument=json.dumps(policy_doc),
    )

def assume_role(base_session, role_arn, session_name="assignment-session"):
    sts = base_session.client("sts")
    resp = sts.assume_role(RoleArn=role_arn, RoleSessionName=session_name)
    c = resp["Credentials"]
    return boto3.Session(
        aws_access_key_id=c["AccessKeyId"],
        aws_secret_access_key=c["SecretAccessKey"],
        aws_session_token=c["SessionToken"],
        region_name=base_session.region_name,
    )

def create_bucket(s3, bucket_name, region):
    # us-east-1 requires omitting LocationConstraint
    if region == "us-east-1" or region is None:
        s3.create_bucket(Bucket=bucket_name)
    else:
        s3.create_bucket(
            Bucket=bucket_name,
            CreateBucketConfiguration={"LocationConstraint": region},
        )

def upload_text_file(s3, bucket, key, content, tmp_dir):
    p = tmp_dir / key
    p.write_text(content, encoding="utf-8")
    s3.upload_file(str(p), bucket, key)

def upload_jpeg_file(s3, bucket, key, tmp_dir):
    p = tmp_dir / key
    p.write_bytes(base64.b64decode(TINY_JPEG_BASE64))
    s3.upload_file(str(p), bucket, key)

def total_size_of_prefix(s3, bucket, prefix):
    paginator = s3.get_paginator("list_objects_v2")
    total = 0
    matched = []
    for page in paginator.paginate(Bucket=bucket, Prefix=prefix):
        for obj in page.get("Contents", []):
            total += obj["Size"]
            matched.append((obj["Key"], obj["Size"]))
    return total, matched

def delete_all_objects_and_bucket(s3, bucket):
    paginator = s3.get_paginator("list_objects_v2")
    batch = []
    for page in paginator.paginate(Bucket=bucket):
        for obj in page.get("Contents", []):
            batch.append({"Key": obj["Key"]})
            if len(batch) == 1000:
                s3.delete_objects(Bucket=bucket, Delete={"Objects": batch})
                batch = []
    if batch:
        s3.delete_objects(Bucket=bucket, Delete={"Objects": batch})

    s3.delete_bucket(Bucket=bucket)

# ----------------------------
# Main flow
# ----------------------------
def main():
    admin_session = boto3.Session(profile_name=ADMIN_PROFILE)
    region = admin_session.region_name or "us-east-1"

    iam = admin_session.client("iam")
    sts = admin_session.client("sts")

    account_id = get_account_id(sts)

    print(f"\n[Admin] Using account_id={account_id}, region={region}")

    # 1) Create IAM user
    user_arn = ensure_iam_user(iam, NEW_IAM_USER_NAME)
    print(f"[Admin] IAM user ready: {NEW_IAM_USER_NAME} ({user_arn})")

    # 2) Create roles with trust policy allowing ONLY this user to assume them
    trust_policy = {
        "Version": "2012-10-17",
        "Statement": [
            {
                "Effect": "Allow",
                "Principal": {"AWS": user_arn},
                "Action": "sts:AssumeRole",
            }
        ],
    }

    dev_role_arn = ensure_role(iam, ROLE_DEV, trust_policy)
    user_role_arn = ensure_role(iam, ROLE_USER, trust_policy)
    print(f"[Admin] Roles ready: Dev={dev_role_arn}, User={user_role_arn}")

    # 3) Attach policies to roles
    # Dev: full S3 access (AWS managed policy)
    attach_managed_policy_if_needed(iam, ROLE_DEV, DEV_MANAGED_POLICY_ARN)

    # User: list/get only (inline policy)
    user_s3_read_policy = {
        "Version": "2012-10-17",
        "Statement": [
            {"Effect": "Allow", "Action": ["s3:ListAllMyBuckets"], "Resource": "*"},
            {"Effect": "Allow", "Action": ["s3:ListBucket"], "Resource": "arn:aws:s3:::*"},
            {"Effect": "Allow", "Action": ["s3:GetObject"], "Resource": "arn:aws:s3:::*/*"},
        ],
    }
    put_inline_role_policy(iam, ROLE_USER, USER_INLINE_POLICY_NAME, user_s3_read_policy)
    print("[Admin] Policies attached: Dev=AmazonS3FullAccess, User=inline list/get")

    # 4) Allow IAM user to assume both roles (user policy)
    assume_roles_policy = {
        "Version": "2012-10-17",
        "Statement": [
            {"Effect": "Allow", "Action": "sts:AssumeRole", "Resource": [dev_role_arn, user_role_arn]}
        ],
    }
    put_inline_user_policy(iam, NEW_IAM_USER_NAME, USER_ASSUME_POLICY_NAME, assume_roles_policy)
    print("[Admin] User policy added: sts:AssumeRole -> Dev & User roles")

    # 5) Create access key for the IAM user (store this!)
    access_key_id, secret_access_key = create_access_key(iam, NEW_IAM_USER_NAME)
    print("\n=== SAVE THESE (only shown once) ===")
    print(f"IAM User: {NEW_IAM_USER_NAME}")
    print(f"AWS_ACCESS_KEY_ID: {access_key_id}")
    print(f"AWS_SECRET_ACCESS_KEY: {secret_access_key}")
    print("===================================\n")

    # 6) Act "as the created user": create a session from the user's keys
    user_base_session = boto3.Session(
        aws_access_key_id=access_key_id,
        aws_secret_access_key=secret_access_key,
        region_name=region,
    )

    # Unique bucket name (global namespace)
    bucket_name = f"cs-assignment-{account_id}-{uuid.uuid4().hex[:8]}"
    print(f"[Plan] Bucket will be: {bucket_name}")

    tmp_dir = Path("./tmp_assignment_files")
    tmp_dir.mkdir(exist_ok=True)

    # 7) Assume Dev role and create bucket + objects
    dev_session = assume_role(user_base_session, dev_role_arn, session_name="dev-session")
    s3_dev = dev_session.client("s3")

    print("\n[Dev] Creating bucket...")
    create_bucket(s3_dev, bucket_name, region)

    print("[Dev] Uploading objects...")
    upload_text_file(s3_dev, bucket_name, "assignment1.txt", "Empty Assignment 1", tmp_dir)
    upload_text_file(s3_dev, bucket_name, "assignment2.txt", "Empty Assignment 2", tmp_dir)
    upload_jpeg_file(s3_dev, bucket_name, "recording1.jpg", tmp_dir)
    print("[Dev] Done uploading: assignment1.txt, assignment2.txt, recording1.jpg")

    # 8) Assume User role and compute total size for keys with prefix "assignment"
    user_session = assume_role(user_base_session, user_role_arn, session_name="user-session")
    s3_user = user_session.client("s3")

    print("\n[User] Listing objects with prefix 'assignment' and summing sizes...")
    total_size, matched = total_size_of_prefix(s3_user, bucket_name, "assignment")
    for k, sz in matched:
        print(f"  - {k}: {sz} bytes")
    print(f"[User] TOTAL size for prefix 'assignment': {total_size} bytes")

    # 9) Assume Dev role again and delete objects + bucket
    dev_session2 = assume_role(user_base_session, dev_role_arn, session_name="dev-session-cleanup")
    s3_dev2 = dev_session2.client("s3")

    print("\n[Dev] Deleting all objects and bucket...")
    delete_all_objects_and_bucket(s3_dev2, bucket_name)
    print("[Dev] Cleanup done: bucket deleted.")

    print("\n✅ Assignment flow completed successfully.")

if __name__ == "__main__":
    main()
```

---

## 2) Run the script

In the same folder where you saved `iam_s3_assignment.py`:

```bash
python iam_s3_assignment.py
```

Expected output (high level):

- Creates IAM user
- Creates `Dev` + `User` roles
- Attaches/creates policies
- Prints IAM user's access key + secret (SAVE THEM)
- Assumes `Dev` → creates bucket + uploads 3 objects
- Assumes `User` → lists objects with prefix `assignment` and prints total size
- Assumes `Dev` → deletes everything (objects + bucket)

---

## 3) How the script matches each requirement

### Requirement 1) Create two IAM roles
- `ensure_role(iam, "Dev", trust_policy)`
- `ensure_role(iam, "User", trust_policy)`

### Requirement 2) Attach IAM policies to roles
- `Dev` gets AWS managed policy: `AmazonS3FullAccess`
- `User` gets inline policy allowing only:
  - `s3:ListAllMyBuckets`
  - `s3:ListBucket`
  - `s3:GetObject`

### Requirement 3) Create IAM user
- `ensure_iam_user(...)`
- `create_access_key(...)`

### Requirement 4) Assume Dev role and create bucket + objects
- `assume_role(user_base_session, dev_role_arn)`
- `create_bucket(...)`
- `upload_file(...)` for `assignment1.txt`, `assignment2.txt`, `recording1.jpg`

### Requirement 5) Assume User role and compute total size of `assignment*`
- `assume_role(user_base_session, user_role_arn)`
- `list_objects_v2` paginator + sum `Size`

### Requirement 6) Assume Dev role and delete objects + bucket
- `delete_objects(...)` (batched)
- `delete_bucket(...)`

---

## 4) Common gotchas / troubleshooting

### Bucket name uniqueness
S3 bucket names are globally unique across all AWS accounts. The script generates one automatically.

### Region special case (us-east-1)
In `us-east-1`, S3 `create_bucket` must NOT include `CreateBucketConfiguration`. The script handles that.

### IAM access key limit
IAM user can have at most **2 access keys**. If you rerun repeatedly, you may hit that limit. Delete an old key in IAM console and rerun.

### Permissions propagation delay
Sometimes IAM changes take ~seconds to propagate. If `AssumeRole` fails right after creation, rerun the script.

---

## 5) Optional: Full cleanup (IAM user + roles)

Some classes only require cleaning S3 resources (bucket/objects). If you also want to delete IAM resources afterward, you can extend the script to:

- Detach `AmazonS3FullAccess` from role `Dev`
- Delete inline role policy from role `User`
- Delete roles `Dev`, `User`
- Delete user inline policy + access keys + the user

(If you want this, tell me and I’ll add a safe cleanup function.)

---

## Security reminder
- Treat printed secrets like passwords.
- Never commit access keys into GitHub.
