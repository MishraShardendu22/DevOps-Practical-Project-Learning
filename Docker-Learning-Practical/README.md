# 🐳 Docker Learning - Practical Guide

Complete hands-on Docker guide from fundamentals to production-ready deployments.

---

## 📚 Learning Path

Follow this structured learning path to master Docker:

### 1️⃣ Fundamentals

#### [01 - Docker Concepts](01-concepts.md)

- What are containers?
- What are images?
- Containerization vs Virtualization
- Hypervisor vs Container Runtime
- When to use VMs vs Containers

#### [02 - Docker Architecture](02-architecture.md)

- Docker Engine components
- Docker Daemon (dockerd)
- Docker CLI
- containerd runtime
- Docker objects (Images, Containers, Networks, Volumes)
- Docker Registry

#### [03 - Installation & Setup](03-installation.md)

- Installation on Fedora/RHEL
- Installation on Ubuntu/Debian
- Installation on AWS EC2
- Docker login credential configuration
- GPG key setup for Linux

---

### 2️⃣ Core Skills

#### [04 - Docker Commands](04-commands.md)

- Authentication & Registry
- Image management (pull, images, rmi, tag, push)
- Container lifecycle (run, start, stop, restart, rm)
- Container inspection (ps, logs, inspect, stats)
- Container interaction (exec, attach, cp)
- System management (info, events, prune)

#### [05 - Dockerfile](05-dockerfile.md)

- Dockerfile structure
- All instructions (FROM, RUN, COPY, ADD, WORKDIR, etc.)
- Best practices for each instruction
- Layer optimization
- Security considerations

---

### 3️⃣ Advanced Topics

#### [06 - Docker Networks](06-Docker-Network.md)

- Network types (host, none, bridge, user-defined bridge)
- Network commands (create, ls, inspect)
- Container DNS
- Network isolation

#### [07 - Docker Volumes](07-Docker-Volumes.md)

- Volume types (named volumes, bind mounts)
- Volume commands (create, ls, inspect)
- Data persistence
- Volume backup strategies

#### [08 - Docker Compose](08-Docker-Compose.md)

- Multi-container orchestration
- docker-compose.yml structure
- Services, networks, volumes
- Compose commands (up, down, logs, ps)
- Best practices

---

### 4️⃣ Production & Optimization

#### [09 - Docker Cleanup](09-Docker-Cleanup.md)

- System prune commands
- Removing unused images, containers, volumes
- Disk usage monitoring
- Cleanup automation

#### [10 - Docker Registry](10-Docker-Registry.md)

- Tagging strategies
- Pushing to Docker Hub
- Private registry setup
- Image versioning

#### [11 - Multi-Stage Builds](11-Multi-Stage-Builds.md)

- Build optimization
- Smaller production images
- Language-specific examples (Node, Go, Python)
- Advanced techniques

#### [12 - Docker Utilities](12-Docker-Utilities.md)

- Background process management
- nohup vs Docker detached mode
- Restart policies
- Resource limits
- systemd integration

---

### 5️⃣ Complete Reference

#### [13 - Summary](13-Summary.md)

- Complete workflow recap
- Best practices checklist
- Production deployment patterns
- Mental models for Docker

---

## 🎯 Quick Reference

### Most Common Commands

```bash
# Pull image
docker pull mongo:latest

# Run container
docker run -d --name myapp -p 8080:80 nginx

# Build image
docker build -t myapp:v1 .

# Push to registry
docker push username/myapp:v1

# Docker Compose
docker compose up -d
docker compose down
```

### Most Common Files

**Dockerfile Example**:

```dockerfile
FROM node:18-alpine
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 3000
CMD ["npm", "start"]
```

**docker-compose.yml Example**:

```yaml
version: "3.9"
services:
  web:
    image: nginx:latest
    ports:
      - "80:80"
  db:
    image: postgres:latest
    volumes:
      - db-data:/var/lib/postgresql/data
volumes:
  db-data:
```

---

## 📖 Additional Resources

- **[Diff SSH vs GPG](Diff-ssh-gpg.md)** - Understanding SSH and GPG key differences
- **[GPG Guide](GPG.md)** - GPG key management
- **[Info](Info.md)** - Additional Docker information

---

## 🎓 Learning Tips

1. **Start Sequential**: Follow the numbered guides in order (01-13)
2. **Practice Each Step**: Don't just read, execute commands
3. **Build Real Projects**: Apply concepts to actual applications
4. **Understand Trade-offs**: Learn when to use each feature
5. **Read Best Practices**: Security and optimization matter

---

## 🚀 What's Next?

After completing this guide:

1. **Kubernetes** - Container orchestration at scale
2. **CI/CD** - GitHub Actions, Jenkins with Docker
3. **Cloud Platforms** - AWS ECS, Google Cloud Run
4. **Security** - Image scanning, secrets management
5. **Monitoring** - Prometheus, Grafana for containers

---
