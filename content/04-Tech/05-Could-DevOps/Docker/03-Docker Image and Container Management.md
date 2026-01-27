## Overview
This guide covers essential commands for managing Docker images and containers. Learn how to list, inspect, remove, and clean up Docker resources efficiently.

**Key Concepts:**
- **Image**: Template/blueprint for containers (like a class)
- **Container**: Running instance of an image (like an object)
- Containers are built from images

---

## Quick Reference

### Images
- List: `docker images`
- Remove: `docker rmi <image_id_or_repo:tag>`
- Remove all: `docker rmi $(docker images -q)`

### Containers
- List running: `docker ps`
- List all: `docker ps -a`
- Stop: `docker stop <id_or_name>`
- Remove: `docker rm <id_or_name>`
- Stop and remove: `docker rm -f <id_or_name>`

---

## Managing Docker Images

### List Images

```bash
# List all images
docker images

# List images with more details
docker images --format "table {{.Repository}}\t{{.Tag}}\t{{.Size}}\t{{.CreatedAt}}"

# List only image IDs
docker images -q

# Filter by repository name
docker images | grep hello-service

# Show disk usage
docker system df
```

### Inspect Images

```bash
# Show detailed information about an image
docker inspect <image_id_or_repo:tag>

# Show image history (layers)
docker history <image_id_or_repo:tag>

# Show image size
docker images <image_id_or_repo:tag>
```

### Remove Images

```bash
# Remove a specific image
docker rmi <image_id_or_repo:tag>

# Remove by image ID
docker rmi <image_id>

# Force remove (even if used by containers)
docker rmi -f <image_id_or_repo:tag>

# Remove multiple images
docker rmi image1:tag1 image2:tag2

# Remove all images
docker rmi $(docker images -q)

# Remove dangling images (untagged)
docker image prune

# Remove all unused images
docker image prune -a
```

### Build Images

```bash
# Build from Dockerfile
docker build -t <image_name>:<tag> .

# Build without cache
docker build --no-cache -t <image_name>:<tag> .

# Build with specific Dockerfile
docker build -f Dockerfile.prod -t <image_name>:<tag> .
```

### Tag Images

```bash
# Tag an image
docker tag <source_image>:<tag> <target_image>:<tag>

# Example: Tag for ECR
docker tag hello-service:latest 123456789012.dkr.ecr.us-east-1.amazonaws.com/hello-service:latest
```

---

## Managing Docker Containers

### List Containers

```bash
# List running containers
docker ps

# List all containers (including stopped)
docker ps -a

# List only container IDs
docker ps -q

# List with custom format
docker ps --format "table {{.ID}}\t{{.Image}}\t{{.Status}}\t{{.Names}}"

# Filter containers
docker ps --filter "name=hello-service"
docker ps --filter "status=running"
docker ps --filter "status=exited"
```

### Inspect Containers

```bash
# Show detailed information about a container
docker inspect <container_id_or_name>

# Show container logs
docker logs <container_id_or_name>

# Follow logs in real-time
docker logs -f <container_id_or_name>

# Show last N lines of logs
docker logs --tail 100 <container_id_or_name>

# Show container resource usage
docker stats <container_id_or_name>

# Show all container stats
docker stats
```

### Start/Stop Containers

```bash
# Start a container
docker start <container_id_or_name>

# Stop a container
docker stop <container_id_or_name>

# Stop container gracefully (SIGTERM, then SIGKILL after timeout)
docker stop <container_id_or_name>

# Force stop (SIGKILL immediately)
docker kill <container_id_or_name>

# Restart a container
docker restart <container_id_or_name>

# Pause a container
docker pause <container_id_or_name>

# Unpause a container
docker unpause <container_id_or_name>
```

### Remove Containers

```bash
# Remove a stopped container
docker rm <container_id_or_name>

# Remove a running container (force)
docker rm -f <container_id_or_name>

# Remove multiple containers
docker rm container1 container2 container3

# Remove all stopped containers
docker container prune

# Remove all containers (stopped and running)
docker rm -f $(docker ps -aq)
```

### Execute Commands in Containers

```bash
# Execute a command in a running container
docker exec <container_id_or_name> <command>

# Open interactive shell
docker exec -it <container_id_or_name> /bin/sh
docker exec -it <container_id_or_name> /bin/bash

# Run command as root
docker exec -u root -it <container_id_or_name> /bin/sh

# Example: Check Java version
docker exec hello-service-container java -version
```

---

## Cleanup Strategies

### Remove Unused Resources

```bash
# Remove all stopped containers
docker container prune

# Remove all unused images
docker image prune -a

# Remove all unused volumes
docker volume prune

# Remove all unused networks
docker network prune

# Remove everything unused (containers, networks, images, build cache)
docker system prune

# Remove everything including volumes (more aggressive)
docker system prune -a --volumes
```

### Clean Up by Pattern

```bash
# Remove containers matching a pattern
docker ps -a | grep "pattern" | awk '{print $1}' | xargs docker rm

# Remove images matching a pattern
docker images | grep "pattern" | awk '{print $3}' | xargs docker rmi

# Example: Remove all hello-service containers
docker ps -a | grep hello-service | awk '{print $1}' | xargs docker rm -f
```

### Disk Usage Analysis

```bash
# Show disk usage summary
docker system df

# Show detailed disk usage
docker system df -v

# Show space used by images
docker images --format "{{.Repository}}:{{.Tag}} - {{.Size}}"
```

---

## Common Workflows

### Complete Cleanup

```bash
# Stop all running containers
docker stop $(docker ps -q)

# Remove all containers
docker rm $(docker ps -aq)

# Remove all images
docker rmi $(docker images -q)

# Or use system prune (safer)
docker system prune -a --volumes
```

### Remove Specific Application

```bash
# 1. Stop containers
docker stop <container_id_or_name>

# 2. Remove containers
docker rm <container_id_or_name>

# 3. Remove images
docker rmi <image_id_or_repo:tag>

# Example:
docker stop hello-service-container
docker rm hello-service-container
docker rmi hello-service:latest
```

### Clean Up After Build Failures

```bash
# Remove dangling images (failed builds)
docker image prune

# Remove build cache
docker builder prune

# Remove all build cache
docker builder prune -a
```

---

## Best Practices

### 1. Regular Cleanup
- Run `docker system prune` periodically to free up disk space
- Remove unused images and containers regularly

### 2. Use Specific Tags
- Avoid using `latest` tag in production
- Use version tags (e.g., `v1.0.0`, `2024-01-15`)

### 3. Stop Before Remove
- Always stop containers before removing them
- Use `docker stop` for graceful shutdown

### 4. Check Before Removing
- Use `docker ps -a` to see all containers before bulk removal
- Use `docker images` to verify images before removal

### 5. Use Prune Commands
- Prefer `docker system prune` over manual removal
- Safer and more efficient

### 6. Monitor Disk Usage
- Regularly check `docker system df`
- Set up alerts for disk space

---

## Troubleshooting

### Common Issues

| Issue | Solution |
|-------|----------|
| "Cannot remove image: image is being used" | Stop/remove containers using the image first |
| "Cannot remove container: container is running" | Stop container first: `docker stop <id>` |
| "No space left on device" | Run `docker system prune -a --volumes` |
| "Image not found" | Check with `docker images` to verify image name/tag |
| "Container name already in use" | Remove existing container or use different name |

### Force Removal

```bash
# Force remove container
docker rm -f <container_id>

# Force remove image
docker rmi -f <image_id>

# Force remove everything
docker system prune -a --volumes --force
```

---

## Related Topics

- [[00-Docker]] - Docker basics and concepts
- [[Dockerlize-Springboot]] - Dockerizing Spring Boot applications
- [[Docker Compose Config]] - Managing containers with Docker Compose
