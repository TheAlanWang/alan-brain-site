**Docker** is a tool that lets you package an app **together with everything it needs to run** (code, libraries, system dependencies, settings) into a **container**, so it runs the same on your laptop, a server, or the cloud.

### Create Docker Compose Configuration

### Build and start
From the project root (where `docker-compose.yml` is), run:
```zsh
# Build and start the container (attached / foreground)
docker-compose up --build

# Build and start in background (detached / background)
docker-compose up --build -d
```
### Close the container
```zsh
# Remove containers/network created by compose
docker-compose down
```

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