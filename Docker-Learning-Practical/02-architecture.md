# Docker Architecture

## Overview

Docker uses a client-server architecture. The Docker client talks to the Docker daemon, which does the heavy lifting of building, running, and distributing Docker containers.

## Architecture Diagram

```md
┌──────────────────────────────────────────────────────────────┐
│                        Docker Host                            │
│                                                               │
│  ┌─────────────┐                                             │
│  │ Docker CLI  │ ──────────────┐                             │
│  │  (Client)   │               │                             │
│  └─────────────┘               │                             │
│                                 ▼                             │
│                          ┌──────────────┐                     │
│                          │ Docker Daemon│                     │
│                          │  (dockerd)   │                     │
│                          └──────┬───────┘                     │
│                                 │                             │
│                                 ▼                             │
│                          ┌──────────────┐                     │
│                          │ containerd   │                     │
│                          │ (runtime)    │                     │
│                          └──────┬───────┘                     │
│                                 │                             │
│                   ┌─────────────┼─────────────┐              │
│                   ▼             ▼             ▼              │
│            ┌───────────┐ ┌───────────┐ ┌───────────┐        │
│            │Container 1│ │Container 2│ │Container 3│        │
│            └───────────┘ └───────────┘ └───────────┘        │
│                                                               │
└──────────────────────────────────────────────────────────────┘
         ▲                                        ▲
         │                                        │
   ┌─────┴──────┐                          ┌─────┴──────┐
   │ Docker Hub │                          │   Local    │
   │ (Registry) │                          │   Images   │
   └────────────┘                          └────────────┘
```md

## Core Components

### 1. Docker Engine

The complete Docker platform consisting of:
- Docker Daemon (dockerd)
- Docker CLI (docker)
- REST API (for communication)
- containerd (container runtime)

**Key Features:**
- Manages containers lifecycle
- Handles images
- Manages networks and volumes
- Provides security isolation

### 2. Docker Daemon (dockerd)

The background service running on the host that manages Docker objects.

**Responsibilities:**
- Listens for Docker API requests
- Manages Docker objects (images, containers, networks, volumes)
- Communicates with other daemons
- Handles container execution through containerd

**Process Flow:**
```md
Client Request → REST API → Docker Daemon → containerd → Container
```md

### 3. Docker CLI (Client)

The command-line interface that users interact with.

**Commands:**
```bash
docker build    # Build images
docker run      # Run containers
docker pull     # Pull images from registry
docker push     # Push images to registry
docker ps       # List containers
docker images   # List images
docker exec     # Execute commands in container
```md

**Communication:**
- Uses REST API to communicate with daemon
- Can connect to remote Docker daemons
- Sends commands, receives responses

### 4. containerd

A high-level container runtime that manages the complete container lifecycle.

**Features:**
- Image transfer and storage
- Container execution and supervision
- Low-level storage management
- Network attachments

**Hierarchy:**
```md
Docker CLI → Docker Daemon → containerd → runc → Container
```md

**runc**: Low-level container runtime that actually creates and runs containers

## Docker Objects

### Images

- Read-only templates for creating containers
- Built from Dockerfiles
- Stored in layers (efficient storage and transfer)
- Can be shared via Docker Registry

### Containers

- Runnable instances of images
- Can be created, started, stopped, moved, or deleted
- Isolated from other containers and host
- Defined by image and configuration options

### Networks

- Allow containers to communicate
- Types:
  - **bridge**: Default network driver
  - **host**: Use host networking
  - **overlay**: Multi-host networking
  - **none**: Disable networking

### Volumes

- Persistent data storage
- Managed by Docker
- Survive container deletion
- Can be shared between containers

## Docker Registry

A storage and distribution system for Docker images.

### Docker Hub (Public Registry)

- Official Docker registry
- Hosts public and private images
- Automatic builds
- Webhooks and integrations

### Private Registry

- Self-hosted registry
- Full control over images
- Enhanced security
- Air-gapped environments

## How Docker Engine Uses Host OS

Unlike VMs, Docker containers share the host OS kernel:

```md
┌─────────────────────────────────────────┐
│         Docker Containers               │
│  ┌─────────┐  ┌─────────┐  ┌─────────┐ │
│  │ App A   │  │ App B   │  │ App C   │ │
│  │ Bins    │  │ Bins    │  │ Bins    │ │
│  │ Libs    │  │ Libs    │  │ Libs    │ │
│  └─────────┘  └─────────┘  └─────────┘ │
└─────────────────────────────────────────┘
              ▲         ▲         ▲
              │         │         │
              └─────────┼─────────┘
                        │
            ┌───────────▼───────────┐
            │    Docker Engine      │
            │    (containerd)       │
            └───────────┬───────────┘
                        │
            ┌───────────▼───────────┐
            │   Host OS Kernel      │
            │   (Linux/Windows)     │
            └───────────┬───────────┘
                        │
            ┌───────────▼───────────┐
            │   Hardware/Server     │
            └───────────────────────┘
```md

**Key Points:**
- All containers share the same kernel
- Each container has isolated:
  - Process space (via namespaces)
  - Network stack
  - File system (via union mounts)
  - Resource limits (via cgroups)

## Isolation Mechanisms

### 1. Namespaces

Provide isolation for:
- **PID**: Process IDs
- **NET**: Network stack
- **MNT**: Mount points
- **UTS**: Hostname and domain
- **IPC**: Inter-process communication
- **USER**: User and group IDs

### 2. Control Groups (cgroups)

Limit and monitor resources:
- CPU usage
- Memory usage
- Disk I/O
- Network bandwidth

### 3. Union File Systems

- Layer images efficiently
- Share common layers between containers
- Fast container creation
- Minimal disk usage

## Docker Architecture Advantages

1. **Efficiency**: Share host OS kernel, minimal overhead
2. **Speed**: Start containers in seconds
3. **Density**: Run many containers on same host
4. **Portability**: Same image runs anywhere
5. **Version Control**: Image layers enable versioning
6. **Modularity**: Microservices architecture support

## Summary

Docker's architecture efficiently manages containers through:
- **Client-server model**: Separation of interface and execution
- **containerd runtime**: Standard container lifecycle management
- **Kernel sharing**: Efficient resource usage via host OS
- **Layered images**: Optimized storage and distribution
- **Strong isolation**: Secure process and resource separation
