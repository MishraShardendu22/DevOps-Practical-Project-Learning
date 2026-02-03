# Dockerfile Guide

## What is a Dockerfile?

A **Dockerfile** is a text file containing instructions to build a Docker image. It automates the process of creating images with all dependencies, configurations, and application code.

**Flow**: `Dockerfile → docker build → Image → docker run → Container`

## Dockerfile Structure

```dockerfile
# Syntax: INSTRUCTION arguments

# Base image
FROM ubuntu:22.04

# Metadata
LABEL maintainer="your.email@example.com"
LABEL version="1.0"

# Install dependencies
RUN apt-get update && apt-get install -y \
    python3 \
    python3-pip \
    && rm -rf /var/lib/apt/lists/*

# Set working directory
WORKDIR /app

# Copy files
COPY . /app

# Install Python packages
RUN pip3 install -r requirements.txt

# Expose port
EXPOSE 8080

# Set environment variables
ENV APP_ENV=production

# Define entrypoint
CMD ["python3", "app.py"]
```

## Dockerfile Instructions

### FROM

Specifies the base image for your build.

```dockerfile
# Official image
FROM ubuntu:22.04

# Alpine (lightweight)
FROM python:3.11-alpine

# Specific version
FROM node:18.17.0

# Multi-stage build
FROM golang:1.20 AS builder
```

**Best Practices:**

- Use official images when possible
- Use specific versions (not `latest`)
- Consider Alpine for smaller images
- Use multi-stage builds for optimization

### RUN

Execute commands during image build.

```dockerfile
# Single command
RUN apt-get update

# Multiple commands (recommended format)
RUN apt-get update && \
    apt-get install -y nginx && \
    rm -rf /var/lib/apt/lists/*

# Install Python packages
RUN pip install --no-cache-dir flask gunicorn

# Create user
RUN useradd -m -u 1000 appuser
```

**Best Practices:**

- Chain commands with `&&` to reduce layers
- Clean up package manager cache
- Use `--no-cache-dir` for pip
- Each RUN creates a new layer

### COPY

Copy files from host to image.

```dockerfile
# Copy single file
COPY app.py /app/

# Copy directory
COPY ./src /app/src

# Copy with wildcard
COPY *.conf /etc/nginx/

# Copy and rename
COPY app.py /app/main.py

# Copy with ownership
COPY --chown=appuser:appuser app.py /app/
```

### ADD

Similar to COPY but with extra features.

```dockerfile
# Copy file
ADD app.py /app/

# Auto-extract tar archives
ADD application.tar.gz /app/

# Download from URL (not recommended)
ADD https://example.com/file.txt /tmp/
```

**Note**: Prefer `COPY` unless you need auto-extraction.

### WORKDIR

Set working directory for subsequent instructions.

```dockerfile
# Set working directory
WORKDIR /app

# Creates directory if it doesn't exist
WORKDIR /app/src/components

# All RUN, CMD, ENTRYPOINT, COPY, ADD will use this directory
```

**Best Practice**: Always use absolute paths.

### EXPOSE

Document which ports the container listens on.

```dockerfile
# Single port
EXPOSE 8080

# Multiple ports
EXPOSE 80 443

# With protocol
EXPOSE 8080/tcp
EXPOSE 53/udp
```

**Note**: This is documentation only. Use `-p` flag with `docker run` to actually publish ports.

### ENV

Set environment variables.

```dockerfile
# Single variable
ENV NODE_ENV=production

# Multiple variables
ENV APP_HOME=/app \
    APP_USER=appuser \
    PORT=8080

# Use in subsequent commands
ENV APP_DIR=/application
WORKDIR $APP_DIR
```

**Use Cases:**

- Configuration
- Path settings
- Feature flags
- API keys (better to pass at runtime)

### ARG

Define build-time variables.

```dockerfile
# Define argument with default
ARG VERSION=1.0
ARG BUILD_DATE

# Use in Dockerfile
FROM ubuntu:${VERSION}
LABEL build_date=${BUILD_DATE}

# Can be overridden during build:
# docker build --build-arg VERSION=2.0 .
```

**Difference from ENV:**

- ARG: Build-time only
- ENV: Build-time and runtime

### CMD

Default command to run when container starts.

```dockerfile
# Exec form (preferred)
CMD ["python3", "app.py"]

# Shell form
CMD python3 app.py

# Provide default arguments to ENTRYPOINT
CMD ["--port", "8080"]
```

**Notes:**

- Only one CMD per Dockerfile (last one wins)
- Can be overridden with `docker run`
- Executed after container starts

### ENTRYPOINT

Configure container to run as executable.

```dockerfile
# Exec form (preferred)
ENTRYPOINT ["python3", "app.py"]

# With CMD for default arguments
ENTRYPOINT ["python3", "app.py"]
CMD ["--port", "8080"]

# Override with: docker run image --port 3000
```

**ENTRYPOINT vs CMD:**

- ENTRYPOINT: Main command (harder to override)
- CMD: Default arguments (easily overridden)
- Together: ENTRYPOINT + CMD = full command

### VOLUME

Create mount point for persistent data.

```dockerfile
# Single volume
VOLUME /data

# Multiple volumes
VOLUME ["/data", "/logs", "/config"]
```

**Use Cases:**

- Database storage
- Application logs
- Configuration files
- Any data that should persist

### USER

Set user for running commands.

```dockerfile
# Create user
RUN useradd -m -u 1000 appuser

# Switch to user
USER appuser

# All subsequent RUN, CMD, ENTRYPOINT run as this user

# Switch back to root (if needed)
USER root
```

**Best Practice**: Don't run as root in production.

### HEALTHCHECK

Define health check for container.

```dockerfile
# HTTP check
HEALTHCHECK --interval=30s --timeout=3s --retries=3 \
  CMD curl -f http://localhost/ || exit 1

# Custom script
HEALTHCHECK CMD /app/healthcheck.sh

# Disable inherited healthcheck
HEALTHCHECK NONE
```

### LABEL

Add metadata to image.

```dockerfile
LABEL maintainer="your.email@example.com"
LABEL version="1.0"
LABEL description="My awesome application"
LABEL com.example.vendor="ACME Inc"
```

## Complete Examples

### Example 1: Python Flask Application

```dockerfile
# Use official Python runtime as base image
FROM python:3.11-slim

# Set metadata
LABEL maintainer="dev@example.com"
LABEL version="1.0"

# Set working directory
WORKDIR /app

# Copy requirements first (for layer caching)
COPY requirements.txt .

# Install dependencies
RUN pip install --no-cache-dir -r requirements.txt

# Copy application code
COPY . .

# Create non-root user
RUN useradd -m -u 1000 appuser && \
    chown -R appuser:appuser /app

# Switch to non-root user
USER appuser

# Expose port
EXPOSE 5000

# Set environment variables
ENV FLASK_APP=app.py
ENV FLASK_ENV=production

# Health check
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD curl -f http://localhost:5000/health || exit 1

# Run application
CMD ["python", "-m", "flask", "run", "--host=0.0.0.0"]
```

### Example 2: Node.js Application

```dockerfile
FROM node:18-alpine

# Set working directory
WORKDIR /usr/src/app

# Copy package files
COPY package*.json ./

# Install dependencies
RUN npm ci --only=production

# Copy application files
COPY . .

# Create non-root user
RUN addgroup -g 1000 appgroup && \
    adduser -D -u 1000 -G appgroup appuser && \
    chown -R appuser:appgroup /usr/src/app

USER appuser

# Expose port
EXPOSE 3000

# Start application
CMD ["node", "server.js"]
```

### Example 3: Multi-Stage Build (Go Application)

```dockerfile
# Build stage
FROM golang:1.20-alpine AS builder

WORKDIR /build

# Copy go mod files
COPY go.mod go.sum ./
RUN go mod download

# Copy source code
COPY . .

# Build binary
RUN CGO_ENABLED=0 GOOS=linux go build -a -installsuffix cgo -o app .

# Runtime stage
FROM alpine:latest

# Install ca-certificates for HTTPS
RUN apk --no-cache add ca-certificates

WORKDIR /root/

# Copy binary from builder
COPY --from=builder /build/app .

# Expose port
EXPOSE 8080

# Run binary
CMD ["./app"]
```

### Example 4: Nginx Web Server

```dockerfile
FROM nginx:alpine

# Copy custom nginx config
COPY nginx.conf /etc/nginx/nginx.conf

# Copy static files
COPY ./dist /usr/share/nginx/html

# Expose port
EXPOSE 80

# Nginx runs in foreground by default
CMD ["nginx", "-g", "daemon off;"]
```

## Building Docker Images

### Basic Build

```bash
# Build from current directory
docker build -t myapp:v1 .

# Build from specific Dockerfile
docker build -f Dockerfile.prod -t myapp:prod .

# Build with build arguments
docker build --build-arg VERSION=2.0 -t myapp:v2 .

# Build without cache
docker build --no-cache -t myapp:v1 .

# Build and tag multiple times
docker build -t myapp:v1 -t myapp:latest .
```

### Build Context

```bash
# Current directory
docker build -t myapp .

# Specific directory
docker build -t myapp /path/to/context

# From URL
docker build -t myapp https://github.com/user/repo.git

# From stdin
docker build -t myapp - < Dockerfile
```

### Build Output

```bash
# Show build progress
docker build -t myapp .

# Quiet build (show only IDs)
docker build -q -t myapp .

# Show full build context
docker build --progress=plain -t myapp .
```

## Port Mapping

Port mapping connects host ports to container ports.

```bash
# Map single port (host:container)
docker run -p 8080:80 nginx

# Map to random host port
docker run -p 80 nginx

# Map specific interface
docker run -p 127.0.0.1:8080:80 nginx

# Map multiple ports
docker run -p 8080:80 -p 8443:443 nginx

# Map all exposed ports to random ports
docker run -P nginx
```

**Dockerfile EXPOSE + docker run -p:**

```dockerfile
# Dockerfile
EXPOSE 80 443
```

```bash
# Publish specific ports
docker run -p 8080:80 -p 8443:443 myapp

# Publish all exposed ports
docker run -P myapp
```

## Best Practices

### 1. Use Official Base Images

```dockerfile
# Good
FROM python:3.11-slim

# Avoid
FROM ubuntu
RUN apt-get install python3
```

### 2. Use Specific Tags

```dockerfile
# Good
FROM node:18.17.0-alpine

# Avoid
FROM node:latest
```

### 3. Minimize Layers

```dockerfile
# Good - single layer
RUN apt-get update && \
    apt-get install -y package1 package2 && \
    rm -rf /var/lib/apt/lists/*

# Avoid - multiple layers
RUN apt-get update
RUN apt-get install -y package1
RUN apt-get install -y package2
```

### 4. Leverage Build Cache

```dockerfile
# Good - copy requirements first
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .

# Avoid - cache invalidated on any code change
COPY . .
RUN pip install -r requirements.txt
```

### 5. Use .dockerignore

```md
# .dockerignore
node_modules
.git
.env
*.md
Dockerfile
.dockerignore
```

### 6. Don't Run as Root

```dockerfile
RUN useradd -m appuser
USER appuser
```

### 7. Use Multi-Stage Builds

```dockerfile
# Reduces final image size
FROM node:18 AS builder
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

FROM nginx:alpine
COPY --from=builder /app/dist /usr/share/nginx/html
```

### 8. Clean Up in Same Layer

```dockerfile
# Good
RUN apt-get update && \
    apt-get install -y package && \
    rm -rf /var/lib/apt/lists/*

# Avoid - cleanup in separate layer doesn't reduce size
RUN apt-get update
RUN apt-get install -y package
RUN rm -rf /var/lib/apt/lists/*
```

## Common Patterns

### Environment-Specific Builds

```dockerfile
ARG ENV=production

FROM node:18-alpine
ENV NODE_ENV=${ENV}
COPY . .
RUN if [ "$ENV" = "production" ]; then \
      npm ci --only=production; \
    else \
      npm install; \
    fi
```

### Conditional Logic

```dockerfile
ARG INSTALL_DEV=false

RUN if [ "$INSTALL_DEV" = "true" ]; then \
      pip install -r requirements-dev.txt; \
    fi
```

## Summary

- **Dockerfile** automates image creation
- Use **multi-stage builds** to reduce image size
- **Layer caching** speeds up builds
- Follow **best practices** for security and efficiency
- **Port mapping** connects containers to host network
- **Environment variables** configure container behavior

Practice building Dockerfiles for different application types to master containerization!
