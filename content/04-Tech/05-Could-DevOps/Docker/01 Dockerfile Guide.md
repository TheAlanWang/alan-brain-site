# Dockerfile Guide

## Overview
A **Dockerfile** is a text file that contains instructions for building a Docker image. It defines how to package your application and its dependencies into a container.

**Key Concepts:**
- **Dockerfile**: Recipe/blueprint for building images
- **Image**: Built from Dockerfile using `docker build`
- **Layer**: Each instruction creates a new layer (cached for faster rebuilds)

---

## Basic Structure

A Dockerfile typically follows this pattern:

```dockerfile
# Base image
FROM <base_image>

# Set working directory
WORKDIR /app

# Copy files
COPY <source> <destination>

# Install dependencies
RUN <command>

# Expose ports
EXPOSE <port>

# Run command
CMD ["executable", "param1", "param2"]
```

---

## Common Dockerfile Instructions

### FROM
Specifies the base image to build upon.

```dockerfile
# Use official image
FROM node:18-alpine

# Use specific version
FROM python:3.11-slim

# Multi-stage: name a stage
FROM maven:3.9 AS builder
```

### WORKDIR
Sets the working directory for subsequent instructions.

```dockerfile
WORKDIR /app
# All subsequent commands run in /app
```

### COPY vs ADD
Copy files from host to container.

```dockerfile
# COPY (recommended - simpler)
COPY package.json .
COPY src ./src

# COPY with wildcards
COPY *.jar /app/

# ADD (can extract archives, fetch URLs - use sparingly)
ADD archive.tar.gz /app/
```

**Best Practice**: Use `COPY` unless you need `ADD`'s special features.

### RUN
Execute commands during image build.

```dockerfile
# Single command
RUN apt-get update && apt-get install -y curl

# Multiple commands (use && to chain)
RUN apt-get update \
    && apt-get install -y \
        curl \
        wget \
    && rm -rf /var/lib/apt/lists/*

# Multi-line for readability
RUN npm install \
    && npm run build \
    && npm cache clean --force
```

**Best Practice**: Chain commands with `&&` and clean up in the same RUN to reduce layers.

### ENV
Set environment variables.

```dockerfile
# Single variable
ENV NODE_ENV=production

# Multiple variables
ENV NODE_ENV=production \
    PORT=8080 \
    API_KEY=secret
```

### ARG
Define build-time variables (not available at runtime).

```dockerfile
ARG VERSION=latest
ARG BUILD_DATE

# Use in build
RUN echo "Building version $VERSION"
```

### EXPOSE
Document which ports the container listens on (doesn't actually publish).

```dockerfile
EXPOSE 8080
EXPOSE 3000 8080
```

### CMD
Default command to run when container starts (can be overridden).

```dockerfile
# Exec form (recommended)
CMD ["node", "server.js"]

# Shell form
CMD node server.js
```

### ENTRYPOINT
Command that always runs (CMD becomes arguments to ENTRYPOINT).

```dockerfile
# Exec form
ENTRYPOINT ["docker-entrypoint.sh"]

# With CMD as arguments
ENTRYPOINT ["java", "-jar"]
CMD ["app.jar"]
```

**Difference**: `CMD` can be overridden, `ENTRYPOINT` cannot (without `--entrypoint` flag).

### USER
Switch to a non-root user (security best practice).

```dockerfile
# Create user
RUN groupadd -r appuser && useradd -r -g appuser appuser

# Switch to user
USER appuser
```

### VOLUME
Create a mount point for persistent data.

```dockerfile
VOLUME ["/data"]
```

---

## Multi-Stage Builds

Multi-stage builds allow you to use multiple `FROM` statements to create smaller final images.

### Why Use Multi-Stage Builds?

1. **Smaller images**: Exclude build tools from final image
2. **Security**: Fewer tools = smaller attack surface
3. **Efficiency**: Separate build and runtime environments

### Example: Spring Boot Multi-Stage

```dockerfile
# Stage 1: Build
FROM maven:3.9-eclipse-temurin-17 AS builder

WORKDIR /app

# Copy pom.xml first (for layer caching)
COPY pom.xml .
RUN mvn dependency:go-offline

# Copy source and build
COPY src ./src
RUN mvn clean package -DskipTests

# Stage 2: Runtime
FROM openjdk:17.0.1-jdk-slim

WORKDIR /app

# Copy only the JAR from builder stage
COPY --from=builder /app/target/*.jar app.jar

# Run as non-root user
RUN groupadd -r spring && useradd -r -g spring spring
USER spring

EXPOSE 8080

ENTRYPOINT ["java", "-jar", "app.jar"]
```

### Example: Node.js Multi-Stage

```dockerfile
# Stage 1: Build
FROM node:18-alpine AS builder

WORKDIR /app

COPY package*.json ./
RUN npm ci

COPY . .
RUN npm run build

# Stage 2: Runtime
FROM node:18-alpine

WORKDIR /app

# Copy only built files and production dependencies
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules ./node_modules
COPY package*.json ./

RUN npm prune --production

USER node

EXPOSE 3000

CMD ["node", "dist/index.js"]
```

---

## Best Practices

### 1. Layer Caching Optimization

Order instructions from least to most frequently changing:

```dockerfile
# ✅ Good: Dependencies copied first
COPY package.json .
RUN npm install
COPY . .

# ❌ Bad: Source copied first
COPY . .
RUN npm install
```

### 2. Use Specific Base Image Tags

```dockerfile
# ✅ Good: Specific version
FROM node:18.17.0-alpine

# ❌ Bad: Latest tag (unpredictable)
FROM node:latest
```

### 3. Minimize Layers

Combine related commands:

```dockerfile
# ✅ Good: Single layer
RUN apt-get update && \
    apt-get install -y curl && \
    rm -rf /var/lib/apt/lists/*

# ❌ Bad: Multiple layers
RUN apt-get update
RUN apt-get install -y curl
RUN rm -rf /var/lib/apt/lists/*
```

### 4. Use Non-Root User

```dockerfile
# Create user
RUN groupadd -r appuser && \
    useradd -r -g appuser appuser

# Switch to user
USER appuser
```

### 5. Use .dockerignore

Create `.dockerignore` to exclude unnecessary files:

```
node_modules
.git
.env
*.log
dist
target
```

### 6. Set Resource Limits

Document expected resources:

```dockerfile
# JVM memory settings
ENV JAVA_OPTS="-Xmx512m -Xms256m"

# Node.js memory
ENV NODE_OPTIONS="--max-old-space-size=512"
```

### 7. Use Health Checks

```dockerfile
HEALTHCHECK --interval=30s --timeout=3s \
  CMD curl -f http://localhost:8080/health || exit 1
```

---

## Common Patterns

### Pattern 1: Simple Application

```dockerfile
FROM python:3.11-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

EXPOSE 8000

CMD ["python", "app.py"]
```

### Pattern 2: Multi-Stage with Build Tools

```dockerfile
# Build stage
FROM golang:1.21 AS builder

WORKDIR /app
COPY . .
RUN go build -o app

# Runtime stage
FROM alpine:latest

RUN apk --no-cache add ca-certificates
WORKDIR /root/

COPY --from=builder /app/app .

CMD ["./app"]
```

### Pattern 3: With Environment Variables

```dockerfile
FROM node:18-alpine

WORKDIR /app

# Build-time args
ARG NODE_ENV=production
ARG VERSION

# Runtime env
ENV NODE_ENV=${NODE_ENV} \
    VERSION=${VERSION}

COPY package*.json ./
RUN npm ci --only=production

COPY . .

EXPOSE 3000

CMD ["node", "server.js"]
```

### Pattern 4: With Entrypoint Script

```dockerfile
FROM postgres:15

COPY docker-entrypoint-initdb.d/ /docker-entrypoint-initdb.d/

# Entrypoint script handles initialization
ENTRYPOINT ["docker-entrypoint.sh"]
CMD ["postgres"]
```

---

## Building Images

### Basic Build

```bash
# Build from current directory
docker build -t my-app:latest .

# Build with specific Dockerfile
docker build -f Dockerfile.prod -t my-app:prod .

# Build without cache
docker build --no-cache -t my-app:latest .
```

### Build with Arguments

```bash
# Pass build arguments
docker build \
  --build-arg VERSION=1.0.0 \
  --build-arg NODE_ENV=production \
  -t my-app:latest .
```

### Multi-Stage Build

```bash
# Build all stages
docker build -t my-app:latest .

# Build specific stage
docker build --target builder -t my-app:builder .
```

---

## Troubleshooting

### Common Issues

| Issue | Solution |
|-------|----------|
| "Cannot find Dockerfile" | Ensure Dockerfile is in build context directory |
| "Layer cache not working" | Reorder instructions (least to most changing) |
| "Image too large" | Use multi-stage builds, alpine base images |
| "Permission denied" | Check file permissions, use USER instruction |
| "Build fails at RUN" | Check command syntax, use `--no-cache` to debug |

### Debugging Tips

```bash
# Build with verbose output
docker build --progress=plain -t my-app:latest .

# Inspect image layers
docker history my-app:latest

# Test build at specific stage
docker build --target builder -t my-app:builder .

# Check image size
docker images my-app:latest
```

---

## Related Topics

- [[00-Docker]] - Docker basics and concepts
- [[02-Docker-Compose-Guide]] - Using Docker Compose
- [[Dockerlize-Springboot-Guide]] - Spring Boot specific examples
- [[Docker Image and Container Management]] - Managing built images
