# Docker Basics

**Docker** is a tool that lets you package an app **together with everything it needs to run** (code, libraries, system dependencies, settings) into a **container**, so it runs the same on your laptop, a server, or the cloud.

## Core Concepts

- **Image**: Template/blueprint for containers (like a class)
- **Container**: Running instance of an image (like an object)
- **Dockerfile**: Recipe for building images
- **docker-compose.yml**: Configuration for running multiple containers

---

## Docker Compose

### Build and Start
From the project root (where `docker-compose.yml` is), run:

```bash
# Build and start the container (attached / foreground)
docker compose up --build

# Build and start in background (detached / background)
docker compose up --build -d
```

### Stop Containers

```bash
# Remove containers/network created by compose
docker compose down

# Stop containers (keeps containers, removes network)
docker compose stop
```

---

## Basic Docker Commands

### Container Management

```bash
# View running containers
docker ps

# View all containers (including stopped)
docker ps -a

# View container logs
docker logs <container_name>
docker logs hello-service-container

# Follow logs in real-time
docker logs -f <container_name>

# Execute commands in running container
docker exec -it <container_name> sh
docker exec -it hello-service-container sh

# Inspect container details
docker inspect <container_name>
docker inspect hello-service-container

# View Docker resource usage
docker stats
```

### Image Management

```bash
# List images
docker images

# Build image from Dockerfile
docker build -t <image_name>:<tag> .

# Remove image
docker rmi <image_id_or_repo:tag>
```

---

## Common Workflows

### Build and Run Single Container

```bash
# Build image
docker build -t my-app:latest .

# Run container
docker run -p 8080:8080 my-app:latest

# Run in background
docker run -d -p 8080:8080 my-app:latest
```

### Build and Run with Docker Compose

```bash
# Build and start
docker compose up --build

# Start in background
docker compose up --build -d

# Stop and remove
docker compose down
```

---

## Related Documentation

- [[Docker Compose Config]] - Docker Compose configuration guide
- [[Docker Image and Container Management]] - Advanced image and container management
- [[Dockerlize-Springboot-Guide]] - Dockerizing Spring Boot applications
