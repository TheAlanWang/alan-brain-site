## Setup requirements
Install AWS command line interface
```bash
# mac os
brew install awscli
```

## Configure AWS CLI
### Step 1: Copy AWS credential to computer
```
~/.aws/credentials
```
![[AWSCLIv2_screeshot1_credential.png|200]]
###### Configure AWS CLI
```bash
# create direction
mkdir -p ~/.aws
# create and open file
nano ~/.aws/credentials
# paste
# `Ctrl + O` save -> press `enter`
# `Ctrl + X` exit

# to confirm
cat ~/.aws/credentials
```

### Step 2: create ECR Repository in AWS
ECR (Elastic Container Registry)
URI（Uniform Resource Identifier）
```bash
# save ERC URI
# Repository URI Format:
# [aws_account_id].dkr.ecr.[region].amazonaws.com/[repository_name]
URI: 127770416211.dkr.ecr.us-east-1.amazonaws.com/hello-service
```
another way to retrieve URI
```bash
ECR_URL=$(aws ecr describe-repositories \
  --repository-names hello-service \
  --region us-east-1 \
  --query 'repositories[0].repositoryUri' \
  --output text)

echo "ECR Repository URL: $ECR_URL"
```

### Step 3: Build and Push Docker Image to ECR
```zsh
docker build --platform linux/amd64 -t hello-service:latest .
```
- Locate the `Dockerfile` 
- Package your service code
- Build a **Linux/AMD64** architecture image
- Tag the image as `hello-service:latest`
- The image exists only in your **local Docker on your Mac**

In local to AWS ECR, get address, save to ECR URL
```bash
   # Using AWS CLI
   ECR_URL=$(aws ecr describe-repositories \
     --repository-names hello-service \
     --region us-east-1 \
     --query 'repositories[0].repositoryUri' \
     --output text)
   
   echo "ECR URL: $ECR_URL"
```
In the **us-east-1** region, get the information for the **`hello-service` ECR repository**.
- Your **AWS CLI credentials are valid**
- You have permission to **describe ECR repositories**
- The **region and repository name** you are querying are correct

3. **Authenticate Docker to ECR:**
   ```bash
   # Extract base URL from full repository URL
   ECR_BASE=$(echo $ECR_URL | cut -d'/' -f1)
   
   # Login to ECR
   aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin $ECR_BASE
   
   # see "Login Succeeded"
   ```
**Whether your AWS CLI credentials are valid**
- Because `aws ecr get-login-password` will only return a token if your credentials are correct and you have the required permissions.
**Whether you have permission to log in to this ECR registry** (which usually means you have push/pull permissions)
- Docker uses the token to log in to `$ECR_BASE`; only a successful login will show `Login Succeeded`.

4. **Tag your image for ECR:**
   ```bash
   docker tag hello-service:latest ${ECR_URL}:latest
   ```
**Add a new name/tag to your local image** so that it matches the ECR naming format.
After tagging, the same image will have **two tags**:
- `hello-service:latest` (easy to remember locally)
- `${ECR_URL}:latest` (used for ECR)

![[AWSCLIv2_P2.png]]

5. **Push the image:**
   ```bash
   docker push ${ECR_URL}:latest
   ```

6. **Verify upload:**
```bash
# List images in your repository
aws ecr list-images \
 --repository-name hello-service \
 --region us-east-1 \
 --query 'imageIds[*].imageTag' \
 --output table
# Return
# ------------
# |ListImages|
# +----------+
# |  latest  |
# +----------+
```

Docker successfully pushed to ECR
### Step 4: Create Task Definition
A Task Definition is like a blueprint for running your container.
1. **Navigate to Task Definitions:**
   - In ECS Console, click "Task definitions" in left menu
   - Click **"Create new task definition"**
![[AWSCLIv2_screeshot.png]]

2. **Configure Task Definition:**
   - **Task definition family**: `hello-task`
   - **Launch type**: AWS Fargate
   - **Operating system/Architecture**: Linux/X86_64
   - **CPU**: 0.25 vCPU
   - **Memory**: 0.5 GB
   - **Task role**: Select `LabRole` (pre-created in Learner Lab)
   - **Task execution role**: Select `LabRole`
   - ![[AWS CLI v2_task.png|300]]

3. **Add Container:**
   - Click **"Add container"**
   - **Name**: `hello-container`
   - **Image URI**: Paste your ECR URI with tag (e.g., `123456789012.dkr.ecr.us-east-1.amazonaws.com/hello-service:latest`)
   - **Port mappings**: 
     - Container port: `8080`
     - Protocol: TCP
   - Leave other settings as default
   - Click **"Add"**
   - ![[AWS CLI v2_container.png|400]]

4. **Create the Task Definition:**
   - Review your settings
   - Click **"Create"**

### Step 5: Create ECS Cluster
**Create an ECS cluster** with one purpose: **to provide a place (a logical environment) for running tasks and services later**.

For reasons that I don't yet understand, cluster creation fails the first time you attempt it,
but succeeds on the second try.
Therefore, run step 2 twice. The first time use a dummy name, e.g. "hello-service-dummy".
The second time (which should succeed), use the real name, i.e. "hello-service".

1. **Navigate to Amazon ECS:**
   - Search for "ECS" in AWS Console
   - Click on "Elastic Container Service"

2. **Create Cluster:**
   - Click **"Create Cluster"**
   - **Cluster name**: `hello-cluster`
   - **Infrastructure**: AWS Fargate (serverless) - should be selected by default
   - Leave other settings as default
   - Click **"Create"**

Understanding ECS Clusters
- **Cluster**: Logical grouping of tasks or services
- **Fargate vs EC2**: Fargate is serverless (AWS manages servers), EC2 gives you more control
- **Pricing**: With Fargate, you pay only for the CPU and memory your containers use

AWS **Fargate** is a **container runtime that does not require you to manage servers** (serverless compute for containers).

### Step 6: Run a Task
 **Navigate to your Cluster:**
   - Go to Clusters → `hello-cluster`
   - Click on the **"Tasks"** tab

2. **Run New Task:**
   - Click **"Run new task"**
   - **Task definition family**: Choose `hello-task`
   - **Task definition revision**: Choose latest
   - **Desired tasks**: 1

3. **Compute configuration:**
   - Choose **Capacity provider strategy** -> **Capacity provider strategy** Choose 'Use custom (Advanced)'
   - **Capacity provider**: Fargate Spot (for cost savings) or regular Fargate

4. **Configure Networking:**
   - **Cluster VPC**: Select the default VPC
   - **Subnets**: Select at least one subnet (preferably in us-east-1a)
   - **Security groups**: Click "Edit"
     - Create new security group
     - **Security group name**: `hello-service-sg`
     - **Inbound rules**: Add rule
       - Type: Custom TCP
       - Port range: 8080
       - Source: Anywhere (this will auto-set the CIDR to 0.0.0.0/0)
   - **Public IP**: Turn ON (Important!)
![[Screenshot 2026-01-17 at 2.46.30 PM.png]]

2. **Run the Task:**
   - Click **"Create"** (this will run the task)
   - In the Task list, wait for the task to transition from PROVISIONING → PENDING → RUNNING

### Getting the Public IP
#### Option 1: Using AWS Console
1. **View Task Details:**
   - Click on the task ID (long string)
   - Under "Configuration" section, look for "Public IP"
   - Copy this IP address 

`35.174.213.55`
#### Option 2: Using AWS CLI (Recommended)
1. **Get the task ARN:**
```bash
   TASK_ARN=$(aws ecs list-tasks \
     --cluster hello-cluster \
     --region us-east-1 \
     --query 'taskArns[0]' \
     --output text)
```
add: --region us-east-1 \

2. **Get the ENI (Elastic Network Interface) ID:**
   ```bash
   ENI_ID=$(aws ecs describe-tasks \
     --cluster hello-cluster \
     --region us-east-1 \
     --tasks $TASK_ARN \
     --query 'tasks[0].attachments[0].details[?name==`networkInterfaceId`].value' \
     --output text)
   ```

3. **Get the Public IP (zsh/bash compatible):**
   ```bash
   # Try to get from Association first
   PUBLIC_IP=$(aws ec2 describe-network-interfaces \
     --network-interface-ids $ENI_ID \
     --region us-east-1 \
     --query 'NetworkInterfaces[0].Association.PublicIp' \
     --output text)
   
   # If not found in Association, try direct PublicIp
   if [[ -z "$PUBLIC_IP" ]] || [[ "$PUBLIC_IP" == "None" ]]; then
     PUBLIC_IP=$(aws ec2 describe-network-interfaces \
       --network-interface-ids $ENI_ID \
       --region us-east-1 \
       --query 'NetworkInterfaces[0].PublicIp' \
       --output text)
   fi
   
   # Display the IP
   if [[ -z "$PUBLIC_IP" ]] || [[ "$PUBLIC_IP" == "None" ]]; then
     echo "Error: Could not get public IP. Is the task running with a public IP?"
   else
     echo "Public IP: $PUBLIC_IP"
   fi
   ```

4. **One-liner version (saves to variable):**
   ```bash
   PUBLIC_IP=$(aws ecs list-tasks --cluster hello-cluster --query 'taskArns[0]' --output text | \
   xargs -I {} aws ecs describe-tasks --cluster hello-cluster --tasks {} --query 'tasks[0].attachments[0].details[?name==`networkInterfaceId`].value' --output text | \
   xargs -I {} aws ec2 describe-network-interfaces --network-interface-ids {} --query 'NetworkInterfaces[0].Association.PublicIp' --output text)
   
   echo "Public IP: $PUBLIC_IP"
   ```

5. **Test Your Service:**
   ```bash
   # Using the IP from above
   curl http://$PUBLIC_IP:8080/api/hello
   
   # Test with your name
   curl http://$PUBLIC_IP:8080/api/hello/YourName
   ```
