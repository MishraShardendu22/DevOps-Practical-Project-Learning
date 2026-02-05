# 🎨 Portfolio Dockerfile Projects

Real-world applications Dockerized for production deployment. Each project demonstrates best practices in containerization, multi-stage builds, and production configurations.

---

## 📂 Projects Overview

### 1. MishraShardendu - Full Stack Portfolio (Monorepo)

**Tech Stack**: TypeScript, Turborepo, pnpm  
**Type**: Monorepo with multiple apps and packages

Modern full-stack portfolio built with:
- **Turborepo** for monorepo management
- **Multiple applications** (Frontend, Admin, Blog)
- **Shared packages** for code reuse
- **Biome** for linting and formatting

[📖 View Project →](MishraShardendu/)

---

### 2. MishraShardendu22-Backend-BlogWebsite

**Tech Stack**: TypeScript, Node.js, Drizzle ORM  
**Type**: Blog Backend API

RESTful API for blog management featuring:
- **Drizzle ORM** for database operations
- **TypeScript** for type safety
- **Postman Collection** included
- **API documentation** and examples

[📖 View Project →](MishraShardendu22-Backend-BlogWebsite/)

---

### 3. MishraShardendu22-Backend-PersonalWebsite

**Tech Stack**: Go (Golang)  
**Type**: Personal Website Backend

High-performance backend built with Go:
- **Native Go** for speed and efficiency
- **RESTful API** architecture
- **Postman Collection** for API testing
- **Comprehensive documentation**

[📖 View Project →](MishraShardendu22-Backend-PersonalWebsite/)

---

### 4. MS22 - Modern Next.js Application

**Tech Stack**: Next.js, TypeScript, Turborepo  
**Type**: Full-stack Next.js App

Production-ready Next.js application:
- **Next.js 14+** with App Router
- **TypeScript** throughout
- **Monorepo structure** with Turborepo
- **Modern tooling** (Biome, pnpm)

[📖 View Project →](MS22/)

---

## 🐳 Dockerization Features

Each project includes:

### ✅ Production Dockerfile

- **Multi-stage builds** for optimized images
- **Layer caching** optimization
- **Security best practices**
- **Minimal final image size**

### ✅ Supervisor Configuration

**File**: [supervisord.conf](supervisord.conf)

Process management with Supervisor:
- Multiple services in one container
- Automatic restart on failure
- Log management
- Process monitoring

### ✅ Docker Compose Ready

Easy local development and deployment:
- Service orchestration
- Environment configuration
- Volume management
- Network setup

---

## 🚀 Common Docker Patterns

### Multi-Stage Build Example

```dockerfile
# Build stage
FROM node:20-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

# Production stage
FROM node:20-alpine
WORKDIR /app
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules ./node_modules
EXPOSE 3000
CMD ["npm", "start"]
```

### Supervisor Configuration

```ini
[supervisord]
nodaemon=true

[program:app]
command=npm start
directory=/app
autostart=true
autorestart=true
stdout_logfile=/var/log/supervisor/app.log
stderr_logfile=/var/log/supervisor/app_err.log
```

---

## 📝 Project-Specific Files

### Common Files Across Projects

- **Dockerfile** - Multi-stage build configuration
- **supervisord.conf** - Process management
- **.dockerignore** - Exclude unnecessary files
- **README.md** - Project documentation
- **API documentation** - Postman collections

---

## 🛠️ How to Use

### 1. Navigate to Project

```bash
cd MishraShardendu22-Backend-BlogWebsite
```

### 2. Build Docker Image

```bash
docker build -t blog-backend:latest .
```

### 3. Run Container

```bash
docker run -d -p 3000:3000 --name blog-api blog-backend:latest
```

### 4. Using Docker Compose (if available)

```bash
docker compose up -d
```

---

## 📚 Learning Resources

### Dockerfile Best Practices

- Use multi-stage builds
- Minimize layer count
- Order instructions by change frequency
- Use `.dockerignore`
- Don't run as root user
- Scan for vulnerabilities

### Supervisor Benefits

- **Multiple processes** in one container
- **Auto-restart** failed services
- **Centralized logging**
- **Resource management**

**Note**: While Supervisor works, consider Kubernetes or Docker Compose for production multi-service deployments.

---

## 🎯 Key Takeaways

### For Each Project Type:

**Node.js/TypeScript**:
- Use Alpine images for smaller size
- Install only production dependencies in final stage
- Leverage pnpm for faster installs

**Go Applications**:
- Use scratch or alpine as base
- Compile with `CGO_ENABLED=0`
- Extremely small final images (< 20MB)

**Monorepos**:
- Consider workspace-aware builds
- Optimize layer caching
- Build only what changed

---

## 📖 Related Documentation

- [Multi-Stage Builds](../Docker-Learning-Practical/11-Multi-Stage-Builds.md)
- [Dockerfile Guide](../Docker-Learning-Practical/05-dockerfile.md)
- [Docker Compose](../Docker-Learning-Practical/08-Docker-Compose.md)
- [Supervisor Config](SuperVisorconfig.md)
- [Ideas & Planning](Idea.md)

---

## 🎓 Next Steps

1. **Customize** Dockerfiles for your needs
2. **Optimize** build times with layer caching
3. **Secure** images by scanning vulnerabilities
4. **Deploy** to production platforms
5. **Monitor** container performance

---

**Build once, run anywhere! 🐳**
