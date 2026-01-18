- [[AWS CLI v2| AWS CLI v2 Practical Guide]]
- [[Manual Deploy AWS ECS|Manual Deploy AWS ECS by using AWS CLI]]
- [[Terraform Deploy AWS ECS|Terraform deploy AWS ECS]]
## Concept
### AWS CLI v2
AWS CLI v2 is the second major version of the **Amazon Web Services Command Line Interface**, a tool that lets you interact with AWS services from the terminal instead of using the AWS web console.
### AWS ECS (Elastic Container Service): 
- an AWS-managed **container orchestration service** that allows you to run, manage, and scale Docker containers on AWS infrastructure.
### Terraform
**Terraform** is an **Infrastructure as Code (IaC)** tool created by **HashiCorp**.  
It allows you to **define, provision, and manage cloud infrastructure** using **declarative configuration files**, instead of manually creating resources.


**ECR Repository**: Stores your Docker images (like Docker Hub, but private to AWS)
**ECS Cluster**: Logical grouping of compute resources
**Task Definition**: Blueprint describing how to run your container
**ECS Task**: Running instance of your container
**Fargate**: AWS manages the underlying servers for you