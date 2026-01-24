# AWS CLI User Practical Guide

## 1. What is AWS CLI

**AWS CLI (Command Line Interface)** is a command-line tool that allows you to interact with AWS services **programmatically**, without using the AWS Management Console.

Instead of clicking buttons in the browser, you can:
- Query AWS resources
- Authenticate services (ECR, ECS, EC2)
- Automate workflows
- Build repeatable deployment scripts

AWS CLI communicates directly with AWS APIs.

## 2. Why Use AWS CLI
### Problems with Manual Console Operations
- Error-prone (copy/paste mistakes)
- Not repeatable
- Hard to document
- Impossible to automate

### Benefits of AWS CLI
- **Repeatable**: same command, same result
- **Scriptable**: can be reused in bash/zsh
- **Automatable**: foundation for CI/CD
- **Auditable**: commands act as documentation

## 3. Installation
### macOS
```bash
brew install awscli
```

Verify installation:
```bash
aws --version
```

## 4. AWS Credentials Configuration
AWS CLI needs credentials to authenticate with AWS.
### Credential Location
```text
~/.aws/credentials
```

### Create credentials file
```bash
mkdir -p ~/.aws
nano ~/.aws/credentials
```

Example:
```ini
[default]
aws_access_key_id=YOUR_ACCESS_KEY
aws_secret_access_key=YOUR_SECRET_KEY
```

Verify configuration:
```bash
aws sts get-caller-identity
```


## 5. AWS CLI Command Structure
```bash
aws <service> <command> [options]
```

Example:
```bash
aws ecr describe-repositories --region us-east-1
```

Components:
- **service**: ecr, ecs, ec2, s3
- **command**: list, describe, create, update
- **options**: region, filters, output format

## 6. AWS CLI Usage in ECS + ECR Workflow
### 6.1 Query ECR Repository URI
```bash
aws ecr describe-repositories \
  --repository-names hello-service \
  --region us-east-1 \
  --query 'repositories[0].repositoryUri' \
  --output text
```

Purpose:
- Avoid manual copying from Console
- Prevent typos
- Enable scripting
### 6.2 Authenticate Docker to ECR
```bash
aws ecr get-login-password --region us-east-1 \
| docker login --username AWS --password-stdin <ECR_BASE>
```

Why AWS CLI is required:
- ECR does not use username/password
- AWS CLI generates a **temporary auth token**
- Docker uses this token to authenticate

### 6.3 Verify Image Upload to ECR
```bash
aws ecr list-images \
  --repository-name hello-service \
  --region us-east-1
```

Purpose:
- Confirm Docker image was successfully pushed
- Avoid opening AWS Console

### 6.4 Query ECS Tasks
```bash
aws ecs list-tasks \
  --cluster hello-cluster \
  --region us-east-1
```
Purpose:
- Find running task ARNs
- Avoid navigating ECS Console

### 6.5 Describe ECS Task Details
```bash
aws ecs describe-tasks \
  --cluster hello-cluster \
  --tasks <TASK_ARN> \
  --region us-east-1
```

Used to retrieve:
- Network attachments
- ENI (Elastic Network Interface)
- Task runtime metadata

### 6.6 Retrieve Public IP of ECS Task
```bash
aws ec2 describe-network-interfaces \
  --network-interface-ids <ENI_ID> \
  --region us-east-1 \
  --query 'NetworkInterfaces[0].Association.PublicIp' \
  --output text
```

Purpose:
- Automatically obtain public IP
- Enable scripted service testing with `curl`

## 7. AWS CLI vs AWS Console

|Aspect|AWS CLI|AWS Console|
|---|---|---|
|Automation|Yes|No|
|Repeatability|High|Low|
|Speed|Fast|Slow|
|CI/CD Ready|Yes|No|
|Learning Curve|Medium|Low|


## 8. AWS CLI vs Terraform

|Tool|Purpose|
|---|---|
|AWS Console|Manual UI operations|
|AWS CLI|Scriptable manual operations|
|Terraform|Declarative Infrastructure as Code|

Relationship:
- Terraform internally calls AWS APIs
- AWS CLI is a stepping stone toward Terraform and CI/CD

## 9. Common AWS CLI Errors

### Credentials Not Found
```text
Unable to locate credentials
```

Solution:
- Check `~/.aws/credentials`
- Reconfigure credentials
### Access Denied

```text
AccessDeniedException
```

Solution:
- Verify IAM role permissions
- Ensure correct AWS account / lab role

### Token Expired
```text
ExpiredTokenException
```

Solution:
- Refresh lab credentials
- Reconfigure AWS CLI

## 10. Summary

AWS CLI is a foundational tool for cloud engineers.
In this project, AWS CLI was used to:
- Authenticate with AWS
- Push Docker images to ECR
- Query ECS tasks
- Retrieve public IPs
- Enable repeatable and scriptable workflows

Mastering AWS CLI is a necessary step before moving to Terraform and production-grade automation.