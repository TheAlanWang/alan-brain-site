1. Build the JAR File
2. Create the Dockerfile
3. Create Docker Compose Configuration
4. Build and Run the Docker Container
5. close docker

Next to create AWS ECR

## 1.JAR file
```bash
# Clean previous builds and package
mvn clean package -DskipTests

# The JAR file will be created in target/ directory
# Verify it exists:
ls target/*.jar  # Unix/macOS/Linux
```
![[Dockerlize-Springboot_1.png|300]]

## 2.Docker file
**Dockerfile**：_怎么“做镜像”_（build image 的配方）
```dockerfile
# Stage 1: Build stage using Maven
FROM maven:3.9-eclipse-temurin-17 AS builder

# Set working directory
WORKDIR /app

# Copy Maven configuration
# This allows Docker to cache dependencies separately
COPY pom.xml .

# Download dependencies (cached if pom.xml hasn't changed)
RUN mvn dependency:go-offline

# Copy source code
COPY src ./src

# Build the application
RUN mvn clean package -DskipTests

# Stage 2: Runtime stage using lightweight JDK
FROM openjdk:17.0.1-jdk-slim

# Create non-root user for security
RUN groupadd -g 1000 spring && useradd -u 1000 -g spring -s /bin/sh -M spring

# Set working directory
WORKDIR /app

# Copy JAR from builder stage
COPY --from=builder /app/target/*.jar app.jar

# Change ownership to non-root user
RUN chown -R spring:spring /app

# JVM memory settings for containers
ENV JAVA_OPTS="-Xmx512m -Xms256m"

# Run the application
ENTRYPOINT ["sh", "-c", "java $JAVA_OPTS -jar /app/app.jar"]
```

## 3.  Create Docker Compose Configuration
`docker-compose.yml` 一键启动容器的配置文件

```yml
version: '3.8'

services:
  hello-service:
    # Build from current directory
    build:
      context: .
      dockerfile: Dockerfile

    # Container name for easier identification
    container_name: hello-service-container

    # Port mapping: host:container
    ports:
      - "8080:8080"

    # Environment variables
    environment:
      - SPRING_PROFILES_ACTIVE=docker
      - JAVA_OPTS=-Xmx256m -Xms128m

    # Health check
    healthcheck:
      test: ["CMD", "wget", "--quiet", "--tries=1", "--spider", "http://localhost:8080/api/hello"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 40s

    # Restart policy
    restart: unless-stopped

    # Resource limits
    deploy:
      resources:
        limits:
          cpus: '0.5'
          memory: 512M
        reservations:
          cpus: '0.25'
          memory: 256M
```


## 4. Build and Run the Docker Container
```bash
docker compose up --build
```

当 compose 里某个 service 写了类似：`build: .` 或 `build: { context: ., dockerfile: Dockerfile }`, docker compose up --build` 就会：
1. 去 `build.context` 目录里找 Dockerfile（默认叫 `Dockerfile`，也可以在 compose 里指定别名）
2. 用 Dockerfile build 出一个镜像
3. 再用这个镜像启动容器

`docker compose up` **不一定会重新打包**`

test
```bash
curl -X POST http://localhost:8080/api/echo -d "hello"
hello=%
```


delete image
```bash
# 1) 看看这个容器是谁（可选）
docker ps -a --filter id=74452aaf201a

# 2) 停掉容器（如果在跑）
docker stop 74452aaf201a

# 3) 删除容器
docker rm 74452aaf201a

# 4) 再删除镜像
docker rmi cs6650_assignment-hello-service:latest

```

## 5. close docker
- `docker compose up`: If it just restarts the same container, the data may still be there.
- `docker compose up --build`: If the build creates a new image, Compose will often recreate the container → data inside the container may be lost.
- `docker compose down`: Removes the containers and network → data inside the containers will be lost.
- `docker compose down -v`: Also removes volumes → the data will be lost too (most destructive).