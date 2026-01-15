# Two main files:
- `Dockerfile`
- `docker-compose.yml`

### What each file does
- **Dockerfile**: defines **how to build an image** (how to package/run the app).
- **docker-compose.yml**: defines **how to run one or more services** (ports, env vars, volumes, healthcheck, restart).

## `Dockerfile` Code
### Typical structure for Spring boot
1) **Builder stage** (Maven + [[JDK vs JRE vs JAR#JDK|JDK]])
   - copy `pom.xml` first  
   - download dependencies (cached if `pom.xml` unchanged)  
   - copy `src/` and build JAR (`mvn package`)
1) **Runtime stage** (lightweight JDK/JRE)
   - copy only the built JAR from builder  
   - run `java -jar app.jar`  
   - optionally run as non-root user

### One-sentence description (for README/interview)
I use a **multi-stage Dockerfile**: the first stage builds the Spring Boot app with Maven into a JAR (with dependency caching), and the second stage runs that JAR on a lightweight JDK image for a smaller and more secure container.

```zsh
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
Stage 1: Build stage using Maven

## `docker-compose.yml` Code

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