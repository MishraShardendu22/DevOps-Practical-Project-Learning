# Docker Utilities and Advanced Tips

## nohup with Docker

### Overview

`nohup` (no hang up) allows processes to continue running after the user logs out or the terminal closes.

### Using nohup with Docker

```bash
# Run Docker container with nohup
nohup docker run -d -p 8080:8080 myapp > docker.log 2>&1 &
```

### Check Process

```bash
# View running background processes
jobs

# View process ID
ps aux | grep docker
```

### View Logs

```bash
# Check nohup output
tail -f docker.log
```

## Docker Run in Background

### Better Alternatives to nohup

#### Using -d (detached mode)

```bash
docker run -d --name myapp -p 8080:8080 myapp
```

#### Using Docker Compose

```bash
docker compose up -d
```

#### Using systemd Service

Create a systemd service file for persistent container management:

```ini
[Unit]
Description=My Docker Container
After=docker.service
Requires=docker.service

[Service]
Type=oneshot
RemainAfterExit=yes
ExecStart=/usr/bin/docker run -d --name myapp -p 8080:8080 myapp
ExecStop=/usr/bin/docker stop myapp
ExecStopPost=/usr/bin/docker rm myapp

[Install]
WantedBy=multi-user.target
```

## Useful Docker Utilities

### Container Management

```bash
# Keep container running in background
docker run -d --restart unless-stopped myapp

# Auto-restart on failure
docker run -d --restart on-failure:5 myapp

# Always restart
docker run -d --restart always myapp
```

### Resource Limits

```bash
# Limit memory
docker run -m 512m myapp

# Limit CPU
docker run --cpus="1.5" myapp

# Combine limits
docker run -m 512m --cpus="1.5" myapp
```

## Best Practices

- Use Docker's native `-d` flag instead of `nohup`
- Implement proper restart policies for production containers
- Use Docker Compose or orchestration tools for complex deployments
- Monitor container logs regularly
- Set resource limits to prevent resource exhaustion
