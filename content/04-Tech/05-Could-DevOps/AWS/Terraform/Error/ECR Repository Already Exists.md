**Error Cause:** The ECR repository `cs6650/assignment1` already exists, but it's not recorded in Terraform state, causing the creation attempt to fail.

**Solution:** Import the existing ECR repository into Terraform state.

## Solution: Import Existing ECR Repository to Terraform

### Method 1: Import Existing Resource (Recommended)
```bash
cd terraform-ecs

# Import the existing ECR repository into Terraform state
terraform import aws_ecr_repository.app_repo cs6650/assignment1

# Then run apply again
terraform apply
```

## Why Does This Error Occur?
- The ECR repository `cs6650/assignment1` already exists (you created it manually or pushed images to it previously)
- Terraform state doesn't have a record of this resource
- When Terraform tries to create it, AWS returns an "already exists" error

After importing, Terraform will manage this existing repository instead of trying to create a new one.

## How Terraform Works

### Terraform Doesn't Automatically Check if Resources Already Exist in AWS

Terraform only checks its own state file (`terraform.tfstate`) and doesn't actively query AWS to see if a resource with the same name already exists.

### Workflow

1. **Check State File:** Terraform first checks `terraform.tfstate` to see if there's a resource record

2. **If Not in State:**
   - Attempts to create the resource
   - If a resource with the same name already exists in AWS → Error (like the `RepositoryAlreadyExistsException` you encountered)

3. **If in State:**
   - Checks if the actual resource exists (via API query)
   - Compares if the configuration matches
   - If configuration differs → Updates the resource
   - If resource doesn't exist → Recreates it

### Your Situation

Looking at `terraform.tfstate`, the ECR repository is already in state:
```
"id": "cs6650/assignment1",
"arn": "arn:aws:ecr:us-west-1:646523381354:repository/cs6650/assignment1"
```

This means:
- Terraform already knows this repository exists
- It won't try to create it again
- It will check if the configuration matches

### Summary

| Situation | Terraform Behavior |
| --------- | ------------------ |
| Record exists in state | Checks and updates (if needed) |
| Not in state, doesn't exist in AWS | Creates new resource |
| Not in state, but exists in AWS | Attempts to create → Fails (needs import) |

### Current Status

Your ECR repository is already in Terraform state, so:

- Terraform won't try to create it
- It will check if the configuration matches
- If the configuration differs, it will update the configuration

You can continue running `terraform apply`, and it should no longer report the ECR repository already exists error.
