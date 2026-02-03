# Docker Learning - Practical Guide

## Overview

This directory contains comprehensive documentation for learning Docker, including concepts, architecture, installation, and practical commands.

## Contents

1. **[Docker Concepts](./01-concepts.md)** - Containers vs VMs, images, and fundamental concepts
2. **[Docker Architecture](./02-architecture.md)** - Understanding Docker Engine, containerd, and components
3. **[Installation & Setup](./03-installation.md)** - Installing Docker on Linux and AWS, credential configuration
4. **[Docker Commands](./04-commands.md)** - Complete reference for essential Docker CLI commands
5. **[Dockerfile Guide](./05-dockerfile.md)** - Creating and building Docker images

## Quick Start

```bash
# Login to Docker Hub
docker login

# Pull an image
docker pull ubuntu:latest

# Run a container
docker run -d -p 8080:80 --name myapp nginx

# View running containers
docker ps

# View logs
docker logs myapp

# Execute commands in container
docker exec -it myapp bash
```

## Learning Path

1. Start with Docker Concepts to understand containers vs VMs
2. Learn Docker Architecture to understand how it works
3. Follow Installation guide to set up Docker
4. Practice with Docker Commands
5. Build your own images using Dockerfile Guide

## Prerequisites

- Basic Linux command-line knowledge
- Understanding of virtualization concepts
- Terminal/command prompt access

## Resources

- [Official Docker Documentation](https://docs.docker.com/)
- [Docker Hub](https://hub.docker.com/)
- Practice exercises in this directory
