# Lab 1: Build & Containerize Your First Java Web Service

**Remember to turn off all your instances and resources etc in AWS when you finish each day.**

## Objective
In this lab, you will:
- Build a basic Java Spring Boot application with a **GET /hello** endpoint
- Run it locally and understand Spring Boot's embedded server architecture
- Containerize it using Docker and verify using Docker Compose
- Do your work in groups of two or three so that you can help each other when you get stuck.

This lab will prepare you for building and deploying more complex microservices in future assignments.

---

## Prerequisites & Installation

### Required Software
Before starting, ensure you have the following installed:

1. **Java Development Kit (JDK) 17**
   - **Windows**: Download from [Oracle JDK](https://www.oracle.com/java/technologies/downloads/#java17) or [OpenJDK](https://adoptium.net/)
   - **macOS**: `brew install openjdk@17` or download from above links
   - **Linux**: `sudo apt install openjdk-17-jdk` (Ubuntu/Debian) or `sudo yum install java-17-openjdk-devel` (RHEL/CentOS)
   - Verify: `java -version`

2. **IntelliJ IDEA** (Recommended IDE)
   - Download from [JetBrains](https://www.jetbrains.com/idea/download/)
   - Community Edition is free and sufficient for this course
   - **Why IntelliJ?** Superior Spring Boot integration, intelligent code completion, built-in Maven support, and excellent debugging capabilities
   - Alternative: VS Code with Java Extension Pack

3. **Maven**
   - **Windows**: Download from [Maven website](https://maven.apache.org/download.cgi), extract, and add to PATH
   - **macOS**: `brew install maven`
   - **Linux**: `sudo apt install maven` or `sudo yum install maven`
   - Verify: `mvn -version`
   - **Note**: IntelliJ IDEA includes embedded Maven, but CLI access is recommended

4. **Docker Desktop**
   - **Windows**: Download from [Docker Desktop for Windows](https://docs.docker.com/desktop/install/windows-install/)
     - **Important**: Enable WSL 2 backend for better performance
     - Ensure virtualization is enabled in BIOS
   - **macOS**: Download from [Docker Desktop for Mac](https://docs.docker.com/desktop/install/mac-install/)
   - **Linux**: Follow [Docker Engine installation](https://docs.docker.com/engine/install/)
   - Verify: `docker --version` and `docker-compose --version`

5. **Git** (for version control)
   - **Windows**: Download from [Git for Windows](https://git-scm.com/download/win)
   - **macOS/Linux**: Usually pre-installed, or use package manager
   - Verify: `git --version`

### Windows-Specific Notes
- **Path Configuration**: After installing Java and Maven, add them to your system PATH:
  - Right-click "This PC" → Properties → Advanced system settings → Environment Variables
  - Add `JAVA_HOME` pointing to your JDK installation
  - Add `%JAVA_HOME%\bin` and Maven's `bin` directory to PATH
- **PowerShell vs Command Prompt**: PowerShell is recommended for better Unix-like command support
- **Line Endings**: Configure Git to handle line endings: `git config --global core.autocrlf true`
- **Firewall**: May need to allow Java and Docker through Windows Firewall

---

## Concepts Covered
- Spring Boot fundamentals and architecture
- RESTful API design with `@RestController` and `@GetMapping`
- Spring Boot's embedded Tomcat server (very briefly)
- Dependency management with Maven
- Multi-stage Docker builds for optimized images
- Container orchestration with Docker Compose

---

## Part 1: Create a Simple Web Service

### Step 1: Generate Project with Spring Initializr

#### Option 1: Using IntelliJ IDEA (Recommended)
1. Open IntelliJ IDEA
2. File → New → Project...
3. Under "Generators" on the left pane, select "Spring Boot"
4. Configure the project:
   - **Type:** Maven
   - **Language:** Java
   - **Project SDK:** 17
   - Click "Next"
5. Enter project metadata:
   - **Group:** `com.cs6650`
   - **Artifact:** `hello-service`
   - **Name:** `hello-service`
   - **Package name:** `com.cs6650.helloservice`
   - **Java:** 17
   - Click "Next"
6. In the Dependencies window:
   - Search and add "Spring Web"
   - Search and add "Lombok"
   - Click "Create"
7. IntelliJ will create and open the project automatically - no download or extraction needed!

#### Option 2: Using Web Interface
1. Navigate to [https://start.spring.io](https://start.spring.io)
2. Configure your project with the same settings as above
3. Click "Generate" to download the project ZIP file
4. Extract and open in your IDE

#### Project Configuration:
   - **Project:** Maven
   - **Language:** Java
   - **Spring Boot:** 3.1.x (or latest 3.x version)
   - **Project Metadata:**
     - Group: `com.cs6650`
     - Artifact: `hello-service`
     - Name: `hello-service`
     - Package name: `com.cs6650.helloservice`
     - Packaging: **Jar**
     - Java: **17**
   - **Dependencies:**
     - Spring Web (for REST endpoints)
     - Lombok (for reducing boilerplate code)

3. Click "Generate" to download the project ZIP file

### IntelliJ IDEA Setup (Only if using Option 2)
1. Extract the downloaded ZIP file
2. Open IntelliJ IDEA
3. Select "Open" and navigate to the extracted project folder
4. IntelliJ will automatically detect it as a Maven project and import dependencies
5. Wait for indexing to complete (see progress bar at bottom)

### Understanding the Generated Structure
```
hello-service/
├── src/
│   ├── main/
│   │   ├── java/
│   │   │   └── com/cs6650/helloservice/
│   │   │       └── HelloServiceApplication.java  # Main Spring Boot application class
│   │   └── resources/
│   │       └── application.properties            # Configuration file
│   └── test/                                     # Unit tests
├── pom.xml                                       # Maven configuration
└── mvnw / mvnw.cmd                              # Maven wrapper scripts
```

---

### Step 2: Create the REST Controller

Create a new package `controller` under `src/main/java/com/cs6650/helloservice/` and add `HelloController.java`:

```java
package com.cs6650.helloservice.controller;

import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;
import lombok.extern.slf4j.Slf4j;

@Slf4j  // Lombok annotation for logging
@RestController  // Combines @Controller and @ResponseBody
@RequestMapping("/hello")  // Base path for all endpoints in this controller
public class HelloController {
    
    @GetMapping  // Maps HTTP GET requests to /hello
    public ResponseEntity<String> sayHello() {
        log.info("Hello endpoint called");
        return ResponseEntity.ok("Hello, World!");
    }
    
    // Bonus: Add a personalized greeting
    @GetMapping("/{name}")
    public ResponseEntity<String> sayHelloToName(@PathVariable String name) {
        log.info("Personalized hello called for: {}", name);
        return ResponseEntity.ok(String.format("Hello, %s!", name));
    }
}
```

### Spring Boot Annotations Explained
- **@RestController**: Indicates this class handles REST requests and automatically serializes return values to JSON/XML
- **@RequestMapping**: Defines the base URL path for all methods in the controller
- **@GetMapping**: Maps HTTP GET requests to specific handler methods
- **@PathVariable**: Extracts values from the URL path
- **@Slf4j**: Lombok annotation that generates a logger field

---

### Step 3: Configure Application Properties

Edit `src/main/resources/application.properties`:

```properties
# Server configuration
server.port=8080
server.servlet.context-path=/api

# Logging configuration
logging.level.com.cs6650.helloservice=DEBUG
logging.pattern.console=%d{HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n

# Application info (useful for monitoring)
spring.application.name=hello-service
```

### Understanding Spring Boot's Auto-Configuration
Spring Boot automatically:
- Embeds Tomcat as the default web server, not very visible to you
- Configures component scanning for your package
- Sets up JSON serialization with Jackson
- Enables production-ready features such as health checks

---

### Step 4: Run and Test Locally

#### Using IntelliJ IDEA:
1. Click the green arrow next to the main class or press `Shift+F10`
2. Watch the console for "Started HelloServiceApplication"

#### Using Maven CLI:
```bash
mvn spring-boot:run
```

#### Windows PowerShell Note:
If you encounter execution policy issues:
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
```

#### Test the Endpoints:
```bash
# Basic endpoint
curl http://localhost:8080/api/hello

# Personalized endpoint
curl http://localhost:8080/api/hello/Student

# Windows users without curl can use PowerShell:
Invoke-WebRequest -Uri "http://localhost:8080/api/hello" -UseBasicParsing
```

---

### Step 5: Build the JAR File

```bash
# Clean previous builds and package
mvn clean package -DskipTests

# The JAR file will be created in target/ directory
# Verify it exists:
ls target/*.jar  # Unix/macOS/Linux
dir target\*.jar  # Windows
```

### Understanding the Maven Build Lifecycle
- **clean**: Removes the target directory
- **compile**: Compiles the source code
- **package**: Creates the JAR/WAR file
- **-DskipTests**: Skips test execution (useful for quick builds)

---

## Part 2: Containerize with Docker

### Step 6: Create Multi-Stage Dockerfile

Create a `Dockerfile` in the project root:

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

Depending on the version of Linux, you might need to use ``addgroup`` and ``adduser``
```dockerfile
RUN addgroup -g 1000 spring && adduser -u 1000 -G spring -s /bin/sh -D spring
```


### Dockerfile Best Practices Explained
- **Multi-stage builds**: Reduces final image size by excluding build tools
- **Layer caching**: Copying pom.xml first allows Docker to cache dependencies
- **Security**: Running as non-root user prevents privilege escalation
- **JDK slim**: Lightweight OpenJDK image for smaller container size
- **JVM tuning**: Container-aware memory settings prevent OOM errors

---

### Step 7: Create Docker Compose Configuration

Create `docker-compose.yml` in the project root:

```yaml
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

### Docker Compose Concepts
- **services**: Defines containers to run
- **build**: Specifies how to build the image
- **ports**: Maps container ports to host ports
- **environment**: Sets environment variables
- **healthcheck**: Monitors container health
- **restart**: Defines restart behavior
- **deploy**: Sets resource constraints

---

### Step 8: Build and Run with Docker Compose

```bash
# Build and start the container
docker-compose up --build

# In another terminal, test the endpoint
curl http://localhost:8080/api/hello

# View logs
docker-compose logs -f hello-service

# Stop the container
docker-compose down
```

### Windows Docker Desktop Notes
- Ensure Docker Desktop is running (check system tray)
- If using WSL 2, run commands from WSL terminal for better performance
- May need to enable "Expose daemon on tcp://localhost:2375" in Docker Desktop settings

---

## Submission Requirements

### Custom Endpoint Test
For your submission, you must demonstrate the personalized endpoint working with YOUR name:

1. **Start your Docker container:**
   ```bash
   docker-compose up --build
   ```

2. **In a new terminal, test with YOUR name:**
   ```bash
   # Replace "YourFirstName" with your actual first name
   curl http://localhost:8080/api/hello/YourFirstName
   
   # Example for a student named Alice:
   curl http://localhost:8080/api/hello/Alice
   # Should return: "Hello, Alice!"
   ```

### Screenshot Requirements
Submit a screenshot showing:
1. A successful run of Docker Compose
2. The curl command with YOUR NAME and its successful response

Submit to Canvas.

---

## Troubleshooting Guide

### Common Issues and Solutions

| Issue | Symptoms | Solution |
|-------|----------|----------|
| Port already in use | "bind: address already in use" | Change port in docker-compose.yml or stop conflicting service |
| Build fails | "package does not exist" | Ensure all imports are correct and dependencies are in pom.xml |
| Container exits immediately | Container stops after starting | Check logs: `docker-compose logs hello-service` |
| Cannot connect to Docker | "Cannot connect to Docker daemon" | Ensure Docker Desktop is running |
| Out of memory | Container killed or slow | Increase Docker Desktop memory allocation |
| Windows line endings | Build fails with strange errors | Configure Git: `git config --global core.autocrlf true` |
| Maven dependency errors | "Could not resolve dependencies" | Run `mvn clean install -U` to force update |

### Debugging Commands
```bash
# View running containers
docker ps

# View all containers (including stopped)
docker ps -a

# View container logs
docker logs hello-service-container

# Execute commands in running container
docker exec -it hello-service-container sh

# Inspect container details
docker inspect hello-service-container

# View Docker resource usage
docker stats
```

---

## Additional Resources

### Claude AI
- Get used to using it. Amazon and Facebook now require you to use an AI in interviews.

### Spring Boot Documentation
- [Spring Boot Reference Guide](https://docs.spring.io/spring-boot/docs/current/reference/html/)
- [Spring Web MVC](https://docs.spring.io/spring-framework/reference/web/webmvc.html)
- [Building REST services](https://spring.io/guides/tutorials/rest/)

### Docker Resources
- [Docker Documentation](https://docs.docker.com/)
- [Dockerfile Best Practices](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)
- [Docker Compose File Reference](https://docs.docker.com/compose/compose-file/)

### IntelliJ IDEA Tips
- **Quick Actions**: `Alt+Enter` for quick fixes
- **Code Generation**: `Alt+Insert` to generate getters/setters/constructors
- **Navigation**: `Ctrl+Click` to navigate to definitions
- **Search Everywhere**: Double-tap `Shift`
- **Run Configuration**: `Ctrl+Shift+F10` to run current file
- **Terminal**: `Alt+F12` to open integrated terminal

---

## Next Steps
After completing this lab, you'll be ready to:
- Add database connectivity with Spring Data JPA (not so much in this course)
- Implement more complex REST endpoints
- Add authentication and authorization
- Deploy to cloud platforms (AWS, Azure, GCP)
- Implement microservice patterns

**Remember to turn off all your instances and resources etc in AWS when you finish each day.**