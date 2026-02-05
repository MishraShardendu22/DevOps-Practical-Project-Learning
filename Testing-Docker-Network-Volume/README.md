# 🧪 Testing Docker Network & Volume

Practical examples demonstrating Docker networking and volume management using real-world applications.

---

## 📂 Projects

### 🐉 Dragon Ball API

**Directory**: [Dragon-Ball-Api/](Dragon-Ball-Api/)

A complete full-stack application showcasing Docker best practices:

- **Multi-container setup** with Docker Compose
- **MongoDB** database integration
- **Volume persistence** for data
- **Network configuration** for inter-container communication
- **Health checks** for service readiness
- **Environment variables** for configuration
- **Restart policies** for reliability

[📖 View Dragon Ball API Documentation →](Dragon-Ball-Api/)

---

## 🎯 What You'll Learn

This directory demonstrates practical implementations of:

### Docker Networks

- Creating custom networks
- Container DNS resolution
- Service-to-service communication
- Network isolation

**Example**:
```bash
docker network create dragon-net
```

### Docker Volumes

- Named volumes for persistence
- Data survival across container restarts
- Volume inspection and management

**Example**:
```bash
docker volume create mongo-data
docker volume inspect mongo-data
```

### Docker Compose

- Multi-service orchestration
- Service dependencies (`depends_on`)
- Health check integration
- Environment variable management
- Port mapping and exposure

**Example docker-compose.yml**:
```yaml
version: "3.9"
services:
  api:
    image: shardendumishra22/dragon-ball:latest
    ports:
      - "4000:4000"
    depends_on:
      mongo-db:
        condition: service_healthy
  
  mongo-db:
    image: mongo:latest
    volumes:
      - mongo-data:/data/db
    healthcheck:
      test: ["CMD","mongosh","--eval","db.adminCommand('ping')"]

volumes:
  mongo-data:
```

---

## 🚀 Quick Start

Navigate to the project directory:

```bash
cd Dragon-Ball-Api
```

Start the application:

```bash
docker compose up -d
```

Check status:

```bash
docker compose ps
```

View logs:

```bash
docker compose logs -f
```

Stop the application:

```bash
docker compose down
```

---

## 📝 Key Concepts Demonstrated

### 1. Service Dependencies

Using `depends_on` with health checks ensures:
- MongoDB starts before the API
- API waits for MongoDB to be healthy
- Proper startup order

### 2. Data Persistence

Named volumes ensure:
- Data survives container deletion
- Data can be backed up
- Multiple containers can share data

### 3. Network Communication

Custom networks enable:
- DNS-based service discovery
- Isolation from other containers
- Secure inter-container communication

### 4. Environment Management

Using `.env` files for:
- Secrets (JWT, passwords)
- Configuration (ports, URIs)
- Environment-specific settings

---

## 🔍 Testing

### Verify Network

```bash
docker network ls
docker network inspect dragon-net
```

### Verify Volume

```bash
docker volume ls
docker volume inspect mongo-data
```

### Test Data Persistence

```bash
# Add data to MongoDB
# Stop containers
docker compose down

# Restart containers
docker compose up -d

# Data should still exist
```

---

## 📚 Related Learning

- [Docker Networks](../Docker-Learning-Practical/06-Docker-Network.md)
- [Docker Volumes](../Docker-Learning-Practical/07-Docker-Volumes.md)
- [Docker Compose](../Docker-Learning-Practical/08-Docker-Compose.md)

---

## 🎓 Next Steps

After understanding these examples:

1. **Experiment**: Modify the docker-compose.yml
2. **Add Services**: Include Redis, Nginx, etc.
3. **Custom Networks**: Create multiple isolated networks
4. **Backup Volumes**: Practice volume backup/restore
5. **Scale Services**: Use `docker compose up --scale`

---

**Practice makes perfect! 🚀**
