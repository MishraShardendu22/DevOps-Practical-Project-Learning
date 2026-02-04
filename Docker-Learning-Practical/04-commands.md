# Docker Commands Reference

Complete guide to essential Docker CLI commands with examples and explanations.

## Authentication & Registry

### docker login

Login to Docker registry (Docker Hub or private registry).

```bash
# Interactive login
docker login

# Login with username
docker login -u your-username

# Login with Personal Access Token (PAT) - Recommended
docker login -u your-username
# Paste your PAT when prompted for password

# Login to private registry
docker login registry.example.com

# Logout
docker logout
```

**Use Cases:**

- Required before pushing images to Docker Hub
- Accessing private repositories
- Using PAT is more secure than password

---

## Image Management

### docker pull

Download an image from a registry.

```bash
# Pull latest version
docker pull ubuntu

# Pull specific version/tag
docker pull ubuntu:22.04

# Pull from specific registry
docker pull registry.example.com/myapp:latest

# Pull all tags of an image
docker pull --all-tags ubuntu
```

**Common Tags:**

- `latest`: Most recent stable version
- `alpine`: Lightweight Alpine Linux-based version
- Version numbers: `22.04`, `3.9`, `14.20.0`

### docker images

List all locally stored images.

```bash
# List all images
docker images

# List with full information
docker images --all

# List image IDs only
docker images -q

# Filter images
docker images --filter "dangling=true"

# Format output
docker images --format "table {{.Repository}}\t{{.Tag}}\t{{.Size}}"
```

**Output Columns:**

- **REPOSITORY**: Image name
- **TAG**: Image version
- **IMAGE ID**: Unique identifier
- **CREATED**: When image was created
- **SIZE**: Disk space used

### docker rmi

Remove images from local storage.

```bash
# Remove single image
docker rmi ubuntu:22.04

# Remove by image ID
docker rmi abc123def456

# Force remove (even if containers exist)
docker rmi -f ubuntu:22.04

# Remove multiple images
docker rmi ubuntu:22.04 nginx:alpine

# Remove all unused images
docker image prune

# Remove all images
docker rmi $(docker images -q)
```

### docker tag

Tag an image with a new name.

```bash
# Create new tag
docker tag ubuntu:22.04 myubuntu:v1

# Tag for registry
docker tag myapp:latest registry.example.com/myapp:v1.0

# Multiple tags for same image
docker tag nginx:latest mynginx:stable
docker tag nginx:latest mynginx:production
```

### docker push

Upload an image to a registry.

```bash
# Push to Docker Hub
docker push your-username/myapp:latest

# Push specific version
docker push your-username/myapp:v1.0

# Push to private registry
docker push registry.example.com/myapp:latest
```

**Prerequisites:**

- Must be logged in (`docker login`)
- Image must be tagged with registry/username prefix

---

## Container Lifecycle

### docker run

Create and start a container from an image.

```bash
# Basic run
docker run ubuntu

# Run with name
docker run --name mycontainer ubuntu

# Run in detached mode (background)
docker run -d nginx

# Run interactively with terminal
docker run -it ubuntu bash

# Run with port mapping
docker run -p 8080:80 nginx

# Run with environment variables
docker run -e "ENV=production" -e "DEBUG=false" myapp

# Run with volume mount
docker run -v /host/path:/container/path ubuntu

# Run with resource limits
docker run --memory="512m" --cpus="1.5" myapp

# Combination of options
docker run -itd --name webapp -p 8080:80 -e "ENV=prod" nginx

# Run and remove after exit
docker run --rm ubuntu echo "Hello World"

# Run with restart policy
docker run --restart=always nginx
```

**Common Options:**

- `-d, --detach`: Run in background
- `-it`: Interactive terminal (combine `-i` and `-t`)
- `-p, --publish`: Port mapping (host:container)
- `-e, --env`: Set environment variables
- `-v, --volume`: Mount volume
- `--name`: Assign container name
- `--rm`: Remove container after exit
- `--restart`: Restart policy (no, on-failure, always, unless-stopped)

### docker start

Start stopped containers.

```bash
# Start single container
docker start mycontainer

# Start by container ID
docker start abc123def456

# Start multiple containers
docker start container1 container2

# Start and attach to output
docker start -a mycontainer

# Start interactively
docker start -i mycontainer
```

### docker stop

Stop running containers gracefully.

```bash
# Stop single container
docker stop mycontainer

# Stop with timeout (default 10s)
docker stop -t 30 mycontainer

# Stop multiple containers
docker stop container1 container2 container3

# Stop all running containers
docker stop $(docker ps -q)
```

**Note**: Sends SIGTERM, waits for timeout, then sends SIGKILL if needed.

### docker restart

Restart containers.

```bash
# Restart single container
docker restart mycontainer

# Restart with custom timeout
docker restart -t 30 mycontainer

# Restart multiple containers
docker restart web db cache
```

### docker rm

Remove stopped containers.

```bash
# Remove single container
docker rm mycontainer

# Force remove running container
docker rm -f mycontainer

# Remove multiple containers
docker rm container1 container2

# Remove all stopped containers
docker container prune

# Remove all containers (stopped and running)
docker rm -f $(docker ps -aq)
```

---

## Container Inspection & Management

### docker ps

List containers.

```bash
# List running containers
docker ps

# List all containers (including stopped)
docker ps -a

# List with specific format
docker ps --format "table {{.Names}}\t{{.Status}}\t{{.Ports}}"

# List only container IDs
docker ps -q

# Filter containers
docker ps --filter "status=running"
docker ps --filter "name=web"
docker ps --filter "ancestor=nginx"

# List with size
docker ps -s
```

**Output Columns:**

- **CONTAINER ID**: Unique identifier
- **IMAGE**: Source image
- **COMMAND**: Command running in container
- **CREATED**: When container was created
- **STATUS**: Current state (Up, Exited, etc.)
- **PORTS**: Port mappings
- **NAMES**: Container name

### docker logs

View container logs (stdout/stderr).

```bash
# View all logs
docker logs mycontainer

# Follow logs (like tail -f)
docker logs -f mycontainer

# Show last N lines
docker logs --tail 100 mycontainer

# Show logs with timestamps
docker logs -t mycontainer

# Show logs since specific time
docker logs --since 2024-01-01 mycontainer
docker logs --since 1h mycontainer

# Show logs until specific time
docker logs --until 2024-01-01T12:00:00 mycontainer

# Combine options
docker logs -f --tail 50 -t mycontainer
```

**Use Cases:**

- Debugging application issues
- Monitoring application output
- Tracking errors and exceptions

### docker attach

Attach to a running container's main process.

```bash
# Attach to container
docker attach mycontainer

# Detach without stopping (Ctrl+P, Ctrl+Q)
# Or exit with Ctrl+C (may stop container)
```

**Note** -

- Attaches to main process (PID 1)
- Exiting may stop the container
- For command execution, use `docker exec` instead

### docker exec

Execute command in running container.

```bash
# Run command
docker exec mycontainer ls /app

# Interactive shell
docker exec -it mycontainer bash

# Run as specific user
docker exec -u root mycontainer whoami

# Set working directory
docker exec -w /app mycontainer pwd

# Set environment variables
docker exec -e "VAR=value" mycontainer env

# Run in background
docker exec -d mycontainer /app/background-task.sh

# Common use cases
docker exec -it mycontainer sh          # Alpine Linux shell
docker exec mycontainer cat /etc/hosts  # Read file
docker exec mycontainer ps aux          # Check processes
docker exec mycontainer env             # View environment
```

**Common Options:**

- `-it`: Interactive terminal
- `-d`: Detached (background)
- `-u`: User
- `-w`: Working directory
- `-e`: Environment variable

### docker inspect

View detailed container/image information.

```bash
# Inspect container
docker inspect mycontainer

# Inspect image
docker inspect nginx:latest

# Get specific field
docker inspect --format='{{.State.Status}}' mycontainer
docker inspect --format='{{.NetworkSettings.IPAddress}}' mycontainer
docker inspect --format='{{json .Config.Env}}' mycontainer

# Inspect multiple objects
docker inspect container1 container2 image1
```

**Use Cases:**

- Get IP address
- View environment variables
- Check mount points
- View network configuration
- Debug issues

### docker stats

Display resource usage statistics.

```bash
# Show stats for all running containers
docker stats

# Show stats for specific containers
docker stats container1 container2

# Show stats once (no streaming)
docker stats --no-stream

# Format output
docker stats --format "table {{.Container}}\t{{.CPUPerc}}\t{{.MemUsage}}"
```

**Metrics:**

- CPU %
- Memory usage/limit
- Memory %
- Network I/O
- Block I/O

---

## Advanced Commands

### docker cp

Copy files between container and host.

```bash
# Copy from container to host
docker cp mycontainer:/app/log.txt ./log.txt

# Copy from host to container
docker cp ./config.json mycontainer:/app/config.json

# Copy directory
docker cp mycontainer:/app/logs ./logs

# Copy with following symlinks
docker cp -L mycontainer:/app/link ./link
```

### docker commit

Create image from container.

```bash
# Create image from running container
docker commit mycontainer mynewimage:v1

# With author and message
docker commit -a "Your Name" -m "Added config" mycontainer mynewimage:v1

# Pause container during commit
docker commit -p mycontainer mynewimage:v1
```

### docker diff

Show filesystem changes in container.

```bash
# Show changes
docker diff mycontainer
```

**Output:**

- `A`: File/directory added
- `D`: File/directory deleted
- `C`: File/directory changed

### docker export / import

```bash
# Export container filesystem to tar
docker export mycontainer > container.tar

# Import tar as image
docker import container.tar mynewimage:v1
```

### docker save / load

```bash
# Save image to tar archive
docker save nginx:latest > nginx.tar
docker save -o nginx.tar nginx:latest

# Load image from tar
docker load < nginx.tar
docker load -i nginx.tar
```

**Use Cases:**

- Transfer images without registry
- Backup images
- Air-gapped environments

---

## Network & Volume Commands

### docker network

```bash
# List networks
docker network ls

# Create network
docker network create mynetwork

# Inspect network
docker network inspect mynetwork

# Connect container to network
docker network connect mynetwork mycontainer

# Disconnect container
docker network disconnect mynetwork mycontainer

# Remove network
docker network rm mynetwork
```

### docker volume

```bash
# List volumes
docker volume ls

# Create volume
docker volume create myvolume

# Inspect volume
docker volume inspect myvolume

# Remove volume
docker volume rm myvolume

# Remove unused volumes
docker volume prune
```

---

## System Commands

### docker system

```bash
# Show disk usage
docker system df

# Show detailed disk usage
docker system df -v

# Remove unused data
docker system prune

# Remove all unused data (including volumes)
docker system prune --volumes

# Remove everything
docker system prune -a --volumes
```

### docker info

```bash
# Show system-wide information
docker info
```

### docker version

```bash
# Show Docker version
docker version

# Short version
docker --version
```

---

## Quick Reference Card

| Task | Command |
|------|---------|

| Login | `docker login` |
| Pull image | `docker pull image:tag` |
| List images | `docker images` |
| Run container | `docker run -d -p 8080:80 --name app nginx` |
| List containers | `docker ps -a` |
| View logs | `docker logs -f container` |
| Execute command | `docker exec -it container bash` |
| Stop container | `docker stop container` |
| Start container | `docker start container` |
| Remove container | `docker rm -f container` |
| Remove image | `docker rmi image` |
| System cleanup | `docker system prune -a` |

---

## Summary

This reference covers all essential Docker commands for:

- Authentication and registry operations
- Image management and distribution
- Container lifecycle management
- Monitoring and debugging
- Resource management
- Network and volume operations
