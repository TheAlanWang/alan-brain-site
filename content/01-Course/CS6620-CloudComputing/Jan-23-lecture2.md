```bash
aws --version
```

aws-cli/2.33.2 Python/3.13.11 Darwin/25.2.0 source/arm64

### AWS config
```bash
aws configure

aws configure --profile dev
```

nano ~/.aws/credentials
### List profiles
```bash
aws configure list-profiles
```
dev
default
### To list all S3 buckets in your AWS account using the CLI
```
~ % aws s3 ls --profile dev
2026-01-23 15:39:18 alan-dev-bucket-s3
```


AWS

#### Identity-based IAM Policy

#### Resource-base IAM Policy

#### IAM role
sample: grader, grader can assign to different people