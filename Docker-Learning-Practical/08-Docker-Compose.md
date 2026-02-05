# Docker Compose

## Overview

Docker Compose is a tool for defining and running multi-container Docker applications using a YAML file. It simplifies the process of managing multiple containers, networks, and volumes.

## Why Use Docker Compose?

- **Simplicity**: One command to start entire application stack
- **Reproducibility**: Same environment across dev, staging, production
- **Version Control**: Infrastructure as code in YAML
- **Isolation**: Each project has isolated environment
- **Orchestration**: Manage service dependencies and startup order

## Installation

Docker Compose v2 is included with Docker Desktop. For Linux:

```bash
# Included with docker-compose-plugin
sudo apt-get install docker-compose-plugin

# Verify installation
docker compose version
```

## Essential Commands

### Start Services

```bash
# Start all services (foreground)
docker compose up

# Start in detached mode (background)
docker compose up -d

# Start specific service
docker compose up web

# Force recreate containers
docker compose up --force-recreate

# Build before starting
docker compose up --build
```

### Stop Services

```bash
# Stop services (keep containers)
docker compose stop

# Stop and remove containers, networks
docker compose down

# Stop and remove volumes too
docker compose down -v

# Stop and remove images
docker compose down --rmi all
```

### View Services

```bash
# List running services
docker compose ps

# List all services (including stopped)
docker compose ps -a

# View service logs
docker compose logs

# Follow logs in real-time
docker compose logs -f

# Logs for specific service
docker compose logs -f web

# Tail last N lines
docker compose logs --tail=100 web
```

### Manage Services

```bash
# Restart services
docker compose restart

# Restart specific service
docker compose restart web

# Pause services
docker compose pause

# Unpause services
docker compose unpause

# Execute command in service
docker compose exec web bash

# Run one-off command
docker compose run web npm test
```

### Build and Images

```bash
# Build services
docker compose build

# Build without cache
docker compose build --no-cache

# Build specific service
docker compose build web

# Pull latest images
docker compose pull

# Push images to registry
docker compose push
```

### Scaling

```bash
# Scale service to N instances
docker compose up -d --scale web=3

# Scale multiple services
docker compose up -d --scale web=3 --scale worker=5
```

## docker-compose.yml Structure

### Basic Structure

```yaml
version: '3.9'

services:
  # Service definitions
  
volumes:
  # Volume definitions
  
networks:
  # Network definitions
```

### Complete Example

```yaml
version: '3.9'

services:
  # Web Application
  web:
    build:
      context: ./web
      dockerfile: Dockerfile
    image: myapp:latest
    container_name: web-app
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=production
      - DATABASE_URL=postgres://db:5432/myapp
    env_file:
      - .env
    volumes:
      - ./src:/app/src
      - web-logs:/var/log
    networks:
      - frontend
      - backend
    depends_on:
      db:
        condition: service_healthy
    restart: unless-stopped
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:3000/health"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 40s

  # Database
  db:
    image: postgres:15-alpine
    container_name: postgres-db
    environment:
      POSTGRES_USER: ${DB_USER}
      POSTGRES_PASSWORD: ${DB_PASSWORD}
      POSTGRES_DB: myapp
    volumes:
      - db-data:/var/lib/postgresql/data
      - ./init.sql:/docker-entrypoint-initdb.d/init.sql:ro
    networks:
      - backend
    restart: unless-stopped
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U ${DB_USER}"]
      interval: 10s
      timeout: 5s
      retries: 5

  # Redis Cache
  redis:
    image: redis:7-alpine
    container_name: redis-cache
    ports:
      - "6379:6379"
    volumes:
      - redis-data:/data
    networks:
      - backend
    restart: unless-stopped
    command: redis-server --appendonly yes

  # Nginx Reverse Proxy
  nginx:
    image: nginx:alpine
    container_name: nginx-proxy
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf:ro
      - ./ssl:/etc/nginx/ssl:ro
    networks:
      - frontend
    depends_on:
      - web
    restart: unless-stopped

volumes:
  db-data:
    driver: local
  redis-data:
  web-logs:

networks:
  frontend:
    driver: bridge
  backend:
    driver: bridge
    internal: true  # No external access
```

## Service Configuration Options

### Build Configuration

```yaml
services:
  web:
    build:
      context: ./app
      dockerfile: Dockerfile.prod
      args:
        - NODE_VERSION=18
        - BUILD_ENV=production
      target: production  # Multi-stage build target
      cache_from:
        - myapp:latest
```

### Environment Variables

```yaml
services:
  app:
    environment:
      # Key-value pairs
      - DEBUG=true
      - API_KEY=secret123
      
    # Or dictionary format
    environment:
      DEBUG: "true"
      API_KEY: secret123
      
    # Load from file
    env_file:
      - .env
      - .env.production
```

### Dependencies

```yaml
services:
  web:
    depends_on:
      # Simple dependency
      - db
      
    # Or with conditions
    depends_on:
      db:
        condition: service_healthy
      redis:
        condition: service_started
```

### Resource Limits

```yaml
services:
  app:
    deploy:
      resources:
        limits:
          cpus: '0.50'
          memory: 512M
        reservations:
          cpus: '0.25'
          memory: 256M
```

### Health Checks

```yaml
services:
  app:
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost/health"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 40s
      
    # Disable inherited healthcheck
    healthcheck:
      disable: true
```

### Restart Policies

```yaml
services:
  app:
    restart: no              # Never restart
    restart: always          # Always restart
    restart: on-failure      # Restart on error
    restart: unless-stopped  # Restart unless manually stopped
```

## Environment Files (.env)

```bash
# .env file
POSTGRES_USER=admin
POSTGRES_PASSWORD=secret
POSTGRES_DB=myapp
NODE_ENV=production
API_PORT=3000
```

Reference in compose file:
```yaml
services:
  db:
    environment:
      POSTGRES_USER: ${POSTGRES_USER}
      POSTGRES_PASSWORD: ${POSTGRES_PASSWORD}
```

## Networks

### Custom Networks

```yaml
networks:
  frontend:
    driver: bridge
    ipam:
      config:
        - subnet: 172.20.0.0/16
  
  backend:
    driver: bridge
    internal: true  # No internet access
  
  external-network:
    external: true  # Use existing network
    name: my-pre-existing-network
```

## Volumes

### Volume Configurations

```yaml
volumes:
  # Simple named volume
  data:
  
  # With driver options
  nfs-data:
    driver: local
    driver_opts:
      type: nfs
      o: addr=10.0.0.1,rw
      device: ":/path/to/dir"
  
  # External volume
  existing-data:
    external: true
    name: my-existing-volume
```

## Best Practices

### Development vs Production

**docker-compose.yml** (base configuration):
```yaml
version: '3.9'
services:
  web:
    image: myapp
    volumes:
      - ./src:/app/src
```

**docker-compose.prod.yml** (production overrides):
```yaml
version: '3.9'
services:
  web:
    restart: always
    environment:
      - NODE_ENV=production
```

Use: `docker compose -f docker-compose.yml -f docker-compose.prod.yml up -d`

### Security

- ✅ Use `.env` files for secrets (don't commit)
- ✅ Use Docker secrets for sensitive data in Swarm
- ✅ Scan images for vulnerabilities
- ✅ Run containers as non-root users
- ✅ Use internal networks for backend services
- ❌ Never hardcode secrets in compose file

### Performance

- Use specific image versions, not `latest`
- Leverage build cache
- Use multi-stage builds
- Minimize image size
- Use `.dockerignore`

### Organization

```bash
project/
├── docker-compose.yml
├── docker-compose.override.yml  # Local development
├── docker-compose.prod.yml      # Production
├── .env.example                 # Template
├── .env                         # Local (gitignored)
└── services/
    ├── web/
    │   └── Dockerfile
    └── api/
        └── Dockerfile
```

## Common Patterns

### Full Stack Application

```yaml
version: '3.9'
services:
  frontend:
    build: ./frontend
    ports:
      - "3000:3000"
    depends_on:
      - backend
      
  backend:
    build: ./backend
    ports:
      - "8000:8000"
    depends_on:
      db:
        condition: service_healthy
        
  db:
    image: postgres:15
    volumes:
      - db-data:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD", "pg_isready"]
      
volumes:
  db-data:
```

### Microservices

```yaml
version: '3.9'
services:
  api-gateway:
    image: nginx:alpine
    depends_on:
      - user-service
      - product-service
      
  user-service:
    build: ./services/users
    
  product-service:
    build: ./services/products
    
  shared-db:
    image: postgres:15
```

## Troubleshooting

### View Service Status
```bash
docker compose ps
docker compose logs service-name
```

### Recreate Service
```bash
docker compose up -d --force-recreate service-name
```

### Validate Compose File
```bash
docker compose config
```

### Debug Network Issues
```bash
docker compose exec service-name ping other-service
docker network inspect project_network-name
```

## Summary

- Use Compose for multi-container applications
- Environment files for configuration management
- Health checks for service dependencies
- Override files for different environments
- Named volumes for data persistence
- Custom networks for service isolation
