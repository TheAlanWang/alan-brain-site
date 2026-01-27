# Using Terraform to Deploy ECS

## Overview
This guide walks you through deploying an ECS service on AWS Fargate using Terraform. You'll create an ECR repository, ECS cluster, task definition, and service - all defined as Infrastructure as Code.

**Prerequisites:**
- ✅ ECR repository is ready (or we'll create one)
- ✅ Docker image built and ready to push
- ✅ AWS CLI configured with appropriate credentials
- ✅ Terraform installed (`brew install terraform`)

---

## Quick Start Checklist

1. ✅ Set up Terraform project directory
2. ✅ Create Terraform configuration files
3. ✅ Initialize and validate Terraform
4. ✅ Apply configuration to create infrastructure
5. ✅ Push Docker image to ECR
6. ✅ Get public IP and test the service

---

## Step 1: Set Up Terraform Project

Create a new directory for your Terraform configuration:

```bash
mkdir terraform-ecs
cd terraform-ecs
```

---

## Step 2: Create Terraform Configuration Files

Create the following files in your `terraform-ecs` directory:

### `variables.tf` - Input Variables

Defines input variables for parameterizing the configuration:

```hcl
variable "aws_region" {
  description = "AWS region for resources"
  type        = string
  default     = "us-east-1"
}

variable "service_name" {
  description = "Name of the ECS service"
  type        = string
  default     = "hello-service"
}

variable "ecr_repository_name" {
  description = "Name of the ECR repository"
  type        = string
  default     = "hello-service"
}

variable "desired_count" {
  description = "Number of tasks to run"
  type        = number
  default     = 1
}

variable "container_port" {
  description = "Port the container listens on"
  type        = number
  default     = 8080
}
```

### `main.tf` - Provider and Data Sources

Main configuration with providers and data sources:

```hcl
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
  required_version = ">= 1.0"
}

provider "aws" {
  region = var.aws_region
}

# Data source to get default VPC
data "aws_vpc" "default" {
  default = true
}

# Data source to get default subnets
data "aws_subnets" "default" {
  filter {
    name   = "vpc-id"
    values = [data.aws_vpc.default.id]
  }
}
```

### `ecr.tf` - ECR Repository

ECR repository resource configuration:

```hcl
# Create ECR repository
resource "aws_ecr_repository" "app_repo" {
  name                 = var.ecr_repository_name
  image_tag_mutability = "MUTABLE"

  image_scanning_configuration {
    scan_on_push = false
  }

  tags = {
    Name        = var.ecr_repository_name
    Environment = "dev"
  }
}
```

### `ecs.tf` - ECS Resources

ECS resources including cluster, task definition, and service:

```hcl
# ECS Cluster
resource "aws_ecs_cluster" "main" {
  name = "${var.service_name}-cluster"

  tags = {
    Name        = "${var.service_name}-cluster"
    Environment = "dev"
  }
}

# Task Execution Role (using existing LabRole)
data "aws_iam_role" "lab_role" {
  name = "LabRole"
}

# ECS Task Definition
resource "aws_ecs_task_definition" "app" {
  family                   = "${var.service_name}-task"
  network_mode             = "awsvpc"
  requires_compatibilities = ["FARGATE"]
  cpu                      = "256"
  memory                   = "512"
  execution_role_arn       = data.aws_iam_role.lab_role.arn
  task_role_arn            = data.aws_iam_role.lab_role.arn

  container_definitions = jsonencode([
    {
      name  = "${var.service_name}-container"
      image = "${aws_ecr_repository.app_repo.repository_url}:latest"
      
      portMappings = [
        {
          containerPort = var.container_port
          protocol      = "tcp"
        }
      ]

      logConfiguration = {
        logDriver = "awslogs"
        options = {
          "awslogs-group"         = aws_cloudwatch_log_group.ecs_logs.name
          "awslogs-region"        = var.aws_region
          "awslogs-stream-prefix" = "ecs"
        }
      }
    }
  ])

  tags = {
    Name        = "${var.service_name}-task"
    Environment = "dev"
  }
}

# CloudWatch Log Group
resource "aws_cloudwatch_log_group" "ecs_logs" {
  name              = "/ecs/${var.service_name}"
  retention_in_days = 7

  tags = {
    Name        = "${var.service_name}-logs"
    Environment = "dev"
  }
}

# Security Group (with -tf- suffix to avoid conflicts with manual creation)
resource "aws_security_group" "ecs_service" {
  name        = "${var.service_name}-tf-sg"
  description = "Security group for ${var.service_name} ECS service"
  vpc_id      = data.aws_vpc.default.id

  ingress {
    from_port   = var.container_port
    to_port     = var.container_port
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
    description = "Allow inbound traffic on container port"
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
    description = "Allow all outbound traffic"
  }

  tags = {
    Name        = "${var.service_name}-tf-sg"
    Environment = "dev"
  }
}

# ECS Service
resource "aws_ecs_service" "app" {
  name            = var.service_name
  cluster         = aws_ecs_cluster.main.id
  task_definition = aws_ecs_task_definition.app.arn
  desired_count   = var.desired_count

  capacity_provider_strategy {
    capacity_provider = "FARGATE_SPOT"
    weight            = 100
    base              = 0
  }

  capacity_provider_strategy {
    capacity_provider = "FARGATE"
    weight            = 0
    base              = 0
  }

  network_configuration {
    subnets          = data.aws_subnets.default.ids
    security_groups  = [aws_security_group.ecs_service.id]
    assign_public_ip = true
  }

  tags = {
    Name        = var.service_name
    Environment = "dev"
  }
}
```

### `outputs.tf` - Output Values

Output values for easy access to created resources:

```hcl
output "ecr_repository_url" {
  description = "URL of the ECR repository"
  value       = aws_ecr_repository.app_repo.repository_url
}

output "cluster_name" {
  description = "Name of the ECS cluster"
  value       = aws_ecs_cluster.main.name
}

output "service_name" {
  description = "Name of the ECS service"
  value       = aws_ecs_service.app.name
}

output "get_public_ip_command" {
  description = "Command to get the public IP of the running task"
  value       = <<-EOT
    aws ecs list-tasks --cluster ${aws_ecs_cluster.main.name} --service-name ${aws_ecs_service.app.name} --query 'taskArns[0]' --output text | \
    xargs -I {} aws ecs describe-tasks --cluster ${aws_ecs_cluster.main.name} --tasks {} --query 'tasks[0].attachments[0].details[?name==`networkInterfaceId`].value' --output text | \
    xargs -I {} aws ec2 describe-network-interfaces --network-interface-ids {} --query 'NetworkInterfaces[0].PublicIp' --output text
  EOT
}
```

### `terraform.tfvars` - Variable Values

Variable values to customize the deployment:

```hcl
aws_region          = "us-east-1"
service_name        = "hello-service"
ecr_repository_name = "hello-service-terraform"  # Different name to avoid conflicts
desired_count       = 1
container_port      = 8080
```

---

## Step 3: Initialize and Plan

**Important:** Make sure you are in the `terraform-ecs` folder when running terraform commands!

### 1. Initialize Terraform

```bash
terraform init
```

This downloads the AWS provider and prepares your working directory.

### 2. Validate Configuration

```bash
terraform validate
```

Should return: `Success! The configuration is valid.`

### 3. Preview Changes

```bash
terraform plan
```

This shows what Terraform will create without actually creating it. Review the output carefully.

---

## Step 4: Apply Terraform Configuration

### 1. Apply the Configuration

```bash
terraform apply
```

- Review the planned changes carefully
- Type `yes` when prompted
- Wait for resources to be created (typically 2-3 minutes)

You should see output like:
```
Apply complete! Resources: X added, 0 changed, 0 destroyed.
```

### 2. Push Docker Image to ECR

After Terraform creates the ECR repository, push your Docker image:

```bash
# Get the new ECR URL from Terraform output
NEW_ECR_URL=$(terraform output -raw ecr_repository_url)
echo "New ECR URL: $NEW_ECR_URL"

# Extract base URL for login
ECR_BASE=$(echo $NEW_ECR_URL | cut -d'/' -f1)

# Login to ECR
aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin $ECR_BASE

# Tag your image (replace 'your-image-name' with your actual image name)
docker tag your-image-name:latest ${NEW_ECR_URL}:latest

# Push the image
docker push ${NEW_ECR_URL}:latest
```

### 3. Force New Deployment

After pushing the image, force ECS to pull the new image:

```bash
# Get cluster and service names from Terraform
CLUSTER=$(terraform output -raw cluster_name)
SERVICE=$(terraform output -raw service_name)

# Force new deployment
aws ecs update-service \
  --cluster $CLUSTER \
  --service $SERVICE \
  --force-new-deployment
```

Wait a few minutes for the new task to start running.

---

## Step 5: Get Public IP and Test

### Get the Public IP

Use this script to get the public IP of your running task:

```bash
# Get cluster and service names from Terraform
CLUSTER=$(terraform output -raw cluster_name)
SERVICE=$(terraform output -raw service_name)

# Get task ARN
TASK_ARN=$(aws ecs list-tasks \
  --cluster $CLUSTER \
  --service-name $SERVICE \
  --query 'taskArns[0]' \
  --output text)

# Get ENI ID
ENI_ID=$(aws ecs describe-tasks \
  --cluster $CLUSTER \
  --tasks $TASK_ARN \
  --query 'tasks[0].attachments[0].details[?name==`networkInterfaceId`].value' \
  --output text)

# Get Public IP (with fallback)
PUBLIC_IP=$(aws ec2 describe-network-interfaces \
  --network-interface-ids $ENI_ID \
  --query 'NetworkInterfaces[0].Association.PublicIp' \
  --output text)

if [[ -z "$PUBLIC_IP" ]] || [[ "$PUBLIC_IP" == "None" ]]; then
  PUBLIC_IP=$(aws ec2 describe-network-interfaces \
    --network-interface-ids $ENI_ID \
    --query 'NetworkInterfaces[0].PublicIp' \
    --output text)
fi

echo "Public IP: $PUBLIC_IP"
```

### Test Your Service

```bash
# Basic test
curl http://$PUBLIC_IP:8080/api/hello

# Test with your name
curl http://$PUBLIC_IP:8080/api/hello/YourName
```

---

## Understanding Terraform State

### State File
- **File**: `terraform.tfstate` tracks your infrastructure
- **Important**: Don't delete this file or you'll lose track of resources
- **Best Practice**: In production, store state in S3 with state locking (DynamoDB)

### Common State Commands

```bash
# Show current state
terraform show

# List resources
terraform state list

# Refresh state (sync with actual infrastructure)
terraform refresh
```

---

## Troubleshooting

### Terraform Errors

| Issue | Solution |
|-------|----------|
| "Error acquiring state lock" | Delete `.terraform.lock.hcl` and retry |
| "InvalidParameterException" | Check task definition CPU/memory combinations (valid pairs: 256/512, 512/1024, etc.) |
| "AccessDenied" | Ensure AWS credentials are current and have proper permissions |
| Resources already exist | Use different names in `terraform.tfvars` or run `terraform destroy` first |

### ECS Task Issues

| Issue | Solution |
|-------|----------|
| Task stops immediately | Check CloudWatch logs: `aws logs tail /ecs/your-service-name --follow` |
| Cannot pull image | Ensure image was pushed successfully to ECR |
| Port already in use | Check your container port matches `container_port` variable |
| Task in STOPPED state | Check task logs in CloudWatch for errors |

### Networking Issues

| Issue | Solution |
|-------|----------|
| No public IP assigned | Ensure `assign_public_ip = true` in ECS service configuration |
| Connection refused | Check security group allows port 8080 (or your container port) |
| Connection timeout | Verify task is in RUNNING state in ECS console |

### ECR Push Problems

| Issue | Solution |
|-------|----------|
| "no basic auth credentials" | Re-run AWS configure steps with correct credentials |
| "repository does not exist" | Check ECR repository name matches exactly |
| "EOF" during push | Network timeout - retry the push |

---

## Clean Up Resources

### Destroy Terraform Resources

To avoid charges, clean up when done:

```bash
terraform destroy
# Type 'yes' when prompted
```

This will destroy all resources created by Terraform:
- ECS Service
- ECS Cluster
- Task Definition
- Security Group
- CloudWatch Log Group
- ECR Repository (if you want to keep it, remove it from `ecr.tf` first)

### Verify Cleanup

Check AWS Console:
- ECS: No running tasks or services
- ECR: Repository deleted (or kept if removed from config)
- CloudWatch: Logs will auto-expire based on retention

---

## Key Concepts

### What Terraform Creates

1. **ECR Repository**: Stores your Docker images
2. **ECS Cluster**: Logical grouping for your containers
3. **Task Definition**: Blueprint for your container (CPU, memory, image, ports)
4. **ECS Service**: Manages desired number of running tasks
5. **Security Group**: Firewall rules for network access
6. **CloudWatch Log Group**: Stores container logs

### Terraform Workflow

1. **Write** configuration files (`.tf` files)
2. **Initialize** (`terraform init`) - downloads providers
3. **Plan** (`terraform plan`) - preview changes
4. **Apply** (`terraform apply`) - create/update infrastructure
5. **Destroy** (`terraform destroy`) - remove infrastructure

---

## Next Steps

After successfully deploying:
- Add Application Load Balancer for production traffic
- Implement auto-scaling policies
- Set up CI/CD pipelines with Terraform
- Explore Terraform modules for reusability
- Add monitoring and alerting