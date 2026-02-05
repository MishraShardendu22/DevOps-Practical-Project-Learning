# Docker Cleanup and Maintenance

## Overview

Docker can consume significant disk space over time. Regular cleanup is essential for system maintenance.

## Cleanup Commands

### Remove All Unused Objects

```bash
docker system prune -a
```

This removes:

- All stopped containers
- All networks not used by at least one container
- All images without at least one container associated to them
- All build cache

### Remove All Images

```bash
docker rmi -f $(docker images -a -q)
```

Force remove all images on the system.

### Remove Stopped Containers

```bash
docker container prune
```

### Remove Unused Volumes

```bash
docker volume prune
```

### Remove Unused Networks

```bash
docker network prune
```

### Remove Build Cache

```bash
docker builder prune
```

## Disk Usage Information

### Check Docker Disk Usage

```bash
docker system df
```

### Detailed Disk Usage

```bash
docker system df -v
```

## Best Practices

- Run `docker system prune` regularly to free up space
- Use `-f` flag to skip confirmation prompts in scripts
- Be careful with `-a` flag as it removes all unused images
- Consider using `--filter` options to selectively clean resources
