---
tags:
  - tech/aws
---
**Amazon S3 (Simple Storage Service) is object storage.**
- You store **objects** (files) inside **buckets**
- Each object has a **key** (name) that can look like a path: `docs/2026/a.pdf`
- S3 is **not** a traditional filesystem (no real folders)

Typical use cases: file uploads, backups, logs, static assets, datasets, media, “file storage” for apps.


## 1) Basic conecpt

### 1) Public access is blocked by default (good!)
S3 has **Block Public Access** settings. Keep them ON unless you truly need public objects.

### 2) S3 is not a disk / NAS
- No real directories
- “Renaming folders” is **copy + delete**
- “Folders” are just key **prefixes**

### 3) Permissions are the #1 source of pain
Access is affected by multiple layers:
1) **IAM identity policies** (user/role)
2) **Bucket policy**
3) **Object ACL** (generally avoid)
4) **KMS key policy** (if using SSE-KMS)
5) **VPC endpoint policy** (if using private endpoints)

---

## 2) Recommended configurations by scenario

### A) Private app file storage (most common)
- Bucket: private
- Access: **IAM role** (EC2/ECS/Lambda)
- Encryption: **SSE-S3** (simple) or **SSE-KMS** (more control)
- Optional: **Presigned URLs** for client-side upload/download

### B) Static site / public assets
Recommended: **CloudFront + S3**
- Keep the bucket private
- Let CloudFront read via OAC/OAI

### C) Data lake / analytics
- Prefix layout: `raw/`, `processed/`, `curated/`
- Enable versioning + lifecycle to move older data to cheaper classes (Glacier)

---

## 3) Bucket creation checklist (best practices)

When creating a bucket, decide:

1. **Region**: same as your compute (latency + cost)
2. **Block Public Access**: keep ON by default
3. **Versioning**: ON for important data
4. **Encryption**:
   - **SSE-S3** (AES-256): simplest
   - **SSE-KMS**: more control + auditing (more complexity)
5. **Lifecycle rules**: auto-transition older objects to cheaper storage
6. **Audit**: consider CloudTrail / access logs when needed

---

## 4) AWS CLI: the commands

> Prereq: configure credentials with `aws configure` (or SSO / IAM role).

Replace `my-bucket-123` and paths with yours.

### 4.1 List / inspect

```bash
# list buckets
aws s3 ls

# list objects in a bucket
aws s3 ls s3://my-bucket-123/

# list under a prefix (like a "folder")
aws s3 ls s3://my-bucket-123/docs/ --recursive
```

### 4.2 Upload / download

```bash
# upload a single file
aws s3 cp ./file.pdf s3://my-bucket-123/docs/file.pdf

# download a single file
aws s3 cp s3://my-bucket-123/docs/file.pdf ./file.pdf

# sync a folder to S3 (incremental)
aws s3 sync ./local_folder s3://my-bucket-123/local_folder

# sync from S3 to local
aws s3 sync s3://my-bucket-123/local_folder ./local_folder
```

### 4.3 Copy / move / delete

```bash
# copy within S3
aws s3 cp s3://my-bucket-123/docs/a.pdf s3://my-bucket-123/docs/b.pdf

# move (rename-ish): copy + delete
aws s3 mv s3://my-bucket-123/docs/a.pdf s3://my-bucket-123/archive/a.pdf

# delete one object
aws s3 rm s3://my-bucket-123/docs/file.pdf

# delete everything under a prefix
aws s3 rm s3://my-bucket-123/docs/ --recursive
```

### 4.4 Object metadata

```bash
aws s3api head-object --bucket my-bucket-123 --key docs/file.pdf
```

### 4.5 Presigned URL (temporary access)

```bash
# generate a temporary download link (e.g., 1 hour)
aws s3 presign s3://my-bucket-123/docs/file.pdf --expires-in 3600
```

---

## 5) Security patterns (practical)

### Pattern 1: App role can read/write only a specific prefix
Example: allow access only to:

- `s3://my-bucket-123/user-uploads/*`

Key ideas:
- Object permissions: `s3:GetObject`, `s3:PutObject`, `s3:DeleteObject` on the **object ARN**
- Listing: `s3:ListBucket` on the **bucket ARN**, restricted by `s3:prefix` condition

### Pattern 2: Direct browser upload using presigned URL
Flow:
1) Client asks your API (FastAPI) for an upload URL
2) API returns a presigned PUT/POST URL
3) Client uploads directly to S3
4) API stores the object key in DB and triggers processing

Benefits:
- No large files through your backend
- Faster uploads + less server cost

---

## 6) Encryption options (what to choose)

### SSE-S3 (recommended default)
- Easy, no key management
- Good for most apps

### SSE-KMS
- Fine-grained control + audit trails
- Requires extra permissions (S3 + KMS policies)

### Client-side encryption
- You encrypt before upload
- Maximum control, more dev work

---

## 7) Storage classes & lifecycle (cost control)

Common classes:
- **Standard**: hot data
- **Intelligent-Tiering**: unknown access patterns
- **Standard-IA**: infrequent access
- **Glacier / Deep Archive**: archival (slow retrieval)

Common lifecycle policies:
- After 30 days → Standard-IA
- After 90 days → Glacier
- After 365 days → expire/delete (if allowed)

---

## 8) Versioning (why it matters)

With versioning ON:
- “Deletes” become delete markers
- You can restore older versions after accidental changes
- Slightly higher storage cost, usually worth it

---

## 9) CORS (when browser uploads fail)

If uploading from a browser directly to S3, you may need bucket **CORS**:
- Allow origin: your domain
- Allow methods: `GET`, `PUT`, `POST`
- Allow headers: as needed

---

## 10) Troubleshooting (fast checklist)

### `AccessDenied`
Check in this order:
1) Are you using the right AWS identity/role?
2) Does the IAM policy allow it?
3) Does the bucket policy allow it?
4) If SSE-KMS: does the KMS key policy allow encrypt/decrypt?
5) If using VPC endpoints: is endpoint policy blocking?
6) Is Block Public Access interfering with your intent?

### `SignatureDoesNotMatch` (presigned URL)
Common causes:
- Wrong region
- Clock skew
- Sending headers that weren’t included in the signature

---

## 11) Naming & prefix layout (recommended)

Bucket naming:
- `company-env-region-purpose`
- Example: `alan-dev-uswest2-uploads`

Prefix layout:
- `uploads/{userId}/...`
- `documents/{docId}/original.pdf`
- `documents/{docId}/chunks/0001.json`

---

## 12) (Optional) Typical layout for a document assistant project

A common setup:
- S3 stores: original uploads + derived artifacts (chunks/embeddings/summary)
- Frontend uploads via presigned URL
- Backend: generates presigned URLs, records keys, triggers async processing

