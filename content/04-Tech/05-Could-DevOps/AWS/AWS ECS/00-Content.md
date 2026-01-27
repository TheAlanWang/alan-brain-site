- [[AWS CLI v2| AWS CLI v2 Practical Guide]]
- [[Manual ECS Deployment Guide|Manual Deploy AWS via AWS CLI]]
- [[Terraform Deploy AWS ECS|Terraform deploy AWS ECS]]
## Key Terms
### AWS CLI v2
- AWS CLI v2 is the second major version of the Amazon Web Services **Command Line Interface**, a tool that lets you interact with AWS services from the terminal instead of using the AWS web console.
### AWS ECS (Elastic Container Service): 
- an AWS-managed **container orchestration service** that allows you to run, manage, and scale Docker containers on AWS infrastructure.
### AWS EC2:
- AWS EC2 is an AWS service that provides **resizable virtual servers** in the cloud.
### AWS Fargate:
- AWS Fargate is a **serverless compute engine for containers**.
### Terraform
- **Terraform** is an **Infrastructure as Code (IaC)** tool created by **HashiCorp**.  
- It allows you to **define, provision, and manage cloud infrastructure** using **declarative configuration files**, instead of manually creating resources.
---
## ECS / ECR Core Concepts
### ECR Repository
- A private Docker image registry in AWS that stores your container images (similar to Docker Hub).
### ECS Cluster
- A **logical grouping** where ECS runs tasks/services.
    - With **EC2 launch type**, the cluster has registered EC2 “container instances.”
    - With **Fargate**, the cluster is mainly logical (compute is managed by AWS).

### Task Definition
- A **blueprint** that defines how to run your container(s): image URI, CPU/memory, ports, env vars, logging, and IAM roles.

### ECS Task
- A **running instance** of a task definition (the actual workload consuming compute).
- An ECS Task is a **running instance** created from a Task Definition. It runs one or more containers using images stored in ECR (or another container registry).
- a task is launched from a task definition, so in almost all normal ECS workflows you create the task definition first.
- ![[ECR_TaskDeifinitions.png|600]]
 
### Fargate (in ECS context)
- The compute option where AWS manages the underlying servers; ECS runs your tasks on Fargate without you managing EC2.

> ECR stores images; ECS runs containers; EC2/Fargate provide the compute where tasks actually run.