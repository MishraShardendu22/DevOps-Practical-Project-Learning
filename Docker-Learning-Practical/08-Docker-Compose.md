# Docker Compose

## Overview

Docker Compose is a tool for defining and running multi-container Docker applications using a YAML file.

## Basic Commands

### Start Services

```bash
docker compose up
```

### Start in Detached Mode

```bash
docker compose up -d
```

### Stop and Remove Services

```bash
docker compose down
```

### View Logs

```bash
docker compose logs
```

### List Running Services

```bash
docker compose ps
```

## Basic docker-compose.yml Structure

```yaml
version: '3.8'
services:
  web:
    image: nginx:latest
    ports:
      - "80:80"
    volumes:
      - ./html:/usr/share/nginx/html
    networks:
      - app-network

  database:
    image: postgres:latest
    environment:
      POSTGRES_PASSWORD: example
    volumes:
      - db-data:/var/lib/postgresql/data
    networks:
      - app-network

volumes:
  db-data:

networks:
  app-network:
```

## Best Practices

- Use environment files for sensitive data
- Define networks explicitly for better control
- Use named volumes for persistent data
- Always specify image versions instead of `latest` in production
