# Docker Registry Operations

## Overview

Docker registries store and distribute Docker images. Docker Hub is the default public registry.

## Tagging Images

### Tag an Image

```bash
docker tag <image-name> <repository-name>:<tag>
```

### Examples

```bash
# Tag local image for Docker Hub
docker tag myapp username/myapp:v1.0

# Tag with latest
docker tag myapp username/myapp:latest

# Tag for private registry
docker tag myapp registry.example.com/myapp:v1.0
```

## Pushing Images

### Push to Registry

```bash
docker push <repository-name>:<tag>
```

### Examples - 1

```bash
# Push to Docker Hub
docker push username/myapp:v1.0

# Push to private registry
docker push registry.example.com/myapp:v1.0
```

## Pulling Images

### Pull from Registry

```bash
docker pull <repository-name>:<tag>
```

## Login to Registry

### Docker Hub

```bash
docker login
```

### Private Registry

```bash
docker login registry.example.com
```

## Best Practices

- Always tag images with meaningful version numbers
- Use semantic versioning (e.g., v1.0.0, v1.0.1)
- Tag stable releases with `latest`
- Don't overwrite existing tags in production
- Use private registries for proprietary applications
