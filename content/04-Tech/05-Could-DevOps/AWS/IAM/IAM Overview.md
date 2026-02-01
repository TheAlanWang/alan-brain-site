## Overview
**AWS Identity and Access Management (IAM)** is the service that controls who can access what in your AWS account. It's the foundation of AWS security.

---

## Core Concepts You Should Know

### 1. IAM Users
- **What**: Individual identities (people or applications) that can access AWS resources
- **Use case**: Long-term access for specific people or services
- **Example**: `alan-admin` user for your personal AWS access

### 2. IAM Groups
- **What**: Collections of IAM users
- **Use case**: Apply permissions to multiple users at once
- **Example**: "Developers" group with S3 read/write access

### 3. IAM Roles
- **What**: Temporary credentials that can be assumed by users, services, or applications
- **Use case**: Grant permissions without sharing long-term credentials
- **Key difference**: Roles are assumed temporarily; users have permanent credentials
- **Example**: EC2 instance role, Lambda execution role, cross-account access

### 4. IAM Policies
- **What**: Documents that define permissions (what actions are allowed/denied on which resources)
- **Two types**:
  - **Identity-based policies**: Attached to users, groups, or roles
  - **Resource-based policies**: Attached to resources (e.g., S3 bucket policies)

### 5. Policy Structure
IAM policies are JSON documents with this structure:
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",  // or "Deny"
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::my-bucket/*"
    }
  ]
}
```

### 6. Access Keys
- **What**: Long-term credentials (Access Key ID + Secret Access Key) for programmatic access
- **Use case**: AWS CLI, SDKs, API calls
- **Security**: Store securely, rotate regularly, never commit to code

### 7. Root Account
- **What**: The email/password used to create your AWS account
- **Best practice**: Never use for daily operations; enable MFA; use only for account management

---

## Key Principles

### 1. Least Privilege
- Grant only the minimum permissions needed
- Start restrictive, add permissions as needed

### 2. Separation of Duties
- Different users/roles for different tasks
- Example: Developer role vs. Admin role

### 3. Shared Responsibility
- AWS manages IAM infrastructure
- You manage who has access and what permissions

---

## Common IAM Patterns

### Pattern 1: User with Access Keys (CLI/SDK)
- Create IAM user
- Generate access keys
- Configure AWS CLI/SDK
- **Use case**: Local development, CI/CD pipelines

### Pattern 2: IAM Role for EC2/ECS/Lambda
- Create IAM role with permissions
- Attach role to service
- Service automatically gets temporary credentials
- **Use case**: Applications running on AWS infrastructure

### Pattern 3: Cross-Account Access
- Create role in Account B
- Allow Account A to assume the role
- **Use case**: Centralized management, shared resources

### Pattern 4: Role Assumption
- User assumes a role temporarily
- Gets temporary credentials (STS)
- **Use case**: Elevated permissions for specific tasks

---

## AWS Managed Policies vs. Custom Policies

### AWS Managed Policies
- Pre-built by AWS
- Examples: `AmazonS3FullAccess`, `AmazonEC2ContainerRegistryPowerUser`
- **Pros**: Quick setup, well-tested
- **Cons**: Often too permissive (not least privilege)

### Custom Policies
- You create them
- **Pros**: Precise permissions, follows least privilege
- **Cons**: More work, need to maintain

---

## Security Best Practices

1. ✅ **Enable MFA** for root account and privileged users
2. ✅ **Use IAM roles** instead of access keys when possible (for AWS services)
3. ✅ **Rotate access keys** regularly (every 90 days)
4. ✅ **Use least privilege** - grant minimum necessary permissions
5. ✅ **Monitor with CloudTrail** - track who did what
6. ✅ **Delete unused credentials** - remove old access keys
7. ✅ **Use groups** - manage permissions at group level, not individual users
8. ✅ **Review permissions regularly** - use IAM Access Analyzer

---

## Practical Guide: Setting Up IAM for AWS CLI

### Step 1: Create IAM User and Access Key

#### Create Access Key for IAM User (e.g., `alan-admin`)

1. Navigate to IAM Console:
   - Go to: **IAM → Users → alan-admin → Security credentials → Create access key**

2. Save the access keys securely:
   - **Access Key ID**: Save this immediately
   - **Secret Access Key**: Save this immediately (you won't be able to view it again)

### Step 2: Configure IAM Permissions for ECR

#### Attach ECR Permissions to IAM User

For push/pull Docker images, attach an AWS managed policy:

- **Recommended**: `AmazonEC2ContainerRegistryPowerUser` (most commonly used)
- **Advanced**: Create custom policy for specific repository access (for later)

### Step 3: Configure AWS CLI

#### Default Profile Configuration

Configure AWS CLI with your region (e.g., `us-west-1`):

```bash
aws configure
```

When prompted, enter:
- **AWS Access Key ID**: Your newly created Access Key ID
- **AWS Secret Access Key**: Your newly created Secret Access Key
- **Default region name**: `us-west-1`
- **Default output format**: `json`

#### Named Profile Configuration

To use a named profile (e.g., `alan`):

```bash
aws configure --profile alan
```

Enter the same information:
- **AWS Access Key ID**: Your alan AccessKey
- **AWS Secret Access Key**: Your alan SecretKey
- **Default region name**: `us-west-1`
- **Default output format**: `json`

#### List Configured Profiles

```bash
aws configure list-profiles
```

#### Manual Credentials File Editing

You can also manually edit the credentials file:

```bash
nano ~/.aws/credentials
```

### Step 4: Verify Configuration

#### Verify Identity

Check that your AWS identity is correctly configured:

```bash
aws sts get-caller-identity
```

This should return your IAM user details.

### Step 5: ECR Login

#### Docker Login to ECR

After configuring AWS CLI, authenticate Docker with ECR:

```bash
aws ecr get-login-password --region us-west-1 | docker login --username AWS --password-stdin <your-ecr-registry-url>
```

Or for a specific registry:

```bash
aws ecr get-login-password --region us-west-1 | docker login --username AWS --password-stdin <account-id>.dkr.ecr.us-west-1.amazonaws.com
```

---

## Summary Checklist

1. ✅ Understand IAM core concepts (Users, Groups, Roles, Policies)
2. ✅ Know the difference between identity-based and resource-based policies
3. ✅ Understand when to use roles vs. users
4. ✅ Create IAM user access key
5. ✅ Save access keys securely
6. ✅ Attach ECR permissions to IAM user
7. ✅ Configure AWS CLI (default or named profile)
8. ✅ Verify identity
9. ✅ Login to ECR with Docker
10. ✅ Follow security best practices