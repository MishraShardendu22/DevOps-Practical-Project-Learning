# Docker Learning Summary

This document summarizes everything covered:

* Images & tags
* Volumes
* Networks
* Dockerfile
* Multi-stage builds
* Docker Hub
* Docker Compose
* Best practices

---

## 1. Docker Images & Tags

Pull latest MongoDB image:

```
docker pull mongo
```

`mongo` == `mongo:latest`

Pull specific version:

```
docker pull mongo:7
docker pull mongo:6.0
docker pull mongo:7.0.5
```

Docker automatically selects correct OS/CPU variant using multi-arch manifests.

Force platform (optional):

```
docker pull --platform=linux/amd64 mongo
```

---

## 2. Docker Networks

Create network:

```
docker network create dragon-net
```

Purpose:

* Containers can talk using service names
* Automatic DNS

Example:

```
mongodb://mongo-db:27017
```

---

## 3. Docker Volumes

Create volume:

```
docker volume create mongo-data
```

Mount volume:

```
-v mongo-data:/data/db
```

Purpose:

* Data survives container deletion

---

## 4. Running Mongo with Volume + Network

```
docker run -d \
--name mongo-db \
--network dragon-net \
-v mongo-data:/data/db \
-p 27017:27017 \
mongo
```

---

## 5. Dockerfile Purpose

Dockerfile = blueprint to build image

Used when:

* You want to build your own image

Not required when:

* You only run prebuilt images

---

## 6. Multi-Stage Build Concept

Build stage contains:

* Source code
* Dev dependencies
* Build tools

Runtime stage contains:

* Only compiled output
* Only prod deps

Pattern:

```
FROM big-image AS builder
# build

FROM runtime-image
COPY --from=builder /artifact /artifact
```

---

## 7. Your Multi-Stage Dockerfile

```
FROM node:20-alpine AS builder
WORKDIR /app
COPY package.json pnpm-lock.yaml ./
RUN npm install -g pnpm
RUN pnpm install
COPY . .
RUN pnpm build

FROM node:20-alpine
WORKDIR /app
COPY package.json pnpm-lock.yaml ./
RUN npm install -g pnpm
RUN pnpm install --prod
COPY --from=builder /app/dist ./dist
EXPOSE 3000
CMD ["pnpm","start"]
```

---

## 8. Pushing Image to Docker Hub

Tag:

```
docker tag dragon-ball:latest shardendumishra22/dragon-ball:latest
```

Push:

```
docker push shardendumishra22/dragon-ball:latest
```

Rule:

```
username/image:tag
```

---

## 9. What Docker Compose Is

Compose = Multi-container orchestrator

Replaces many docker run commands with one file + one command.

Does:

* Creates network
* Creates volumes
* Pulls images
* Runs containers

Does NOT:

* Build images unless told
* Replace Dockerfile

---

## 10. Docker Compose vs Dockerfile

| Purpose             | Tool           |
| ------------------- | -------------- |
| Build image         | Dockerfile     |
| Run one container   | docker run     |
| Run many containers | docker compose |

---

## 11. Best-Practice docker-compose.yml

```
version: "3.9"

services:
  mongo-db:
    image: mongo:latest
    container_name: mongo-db
    volumes:
      - mongo-data:/data/db
    healthcheck:
      test: ["CMD","mongosh","--eval","db.adminCommand('ping')"]
      interval: 5s
      timeout: 5s
      retries: 5
      start_period: 10s

  api:
    image: shardendumishra22/dragon-ball:latest
    container_name: dragon-api
    ports:
      - "4000:4000"
    environment:
      MONGO_URI: ${MONGO_URI}
      JWT_SECRET: ${JWT_SECRET}
      ADMIN_USERNAME: ${ADMIN_USERNAME}
      ADMIN_PASSWORD: ${ADMIN_PASSWORD}
      PORT: ${PORT}
    depends_on:
      mongo-db:
        condition: service_healthy
    restart: unless-stopped

volumes:
  mongo-data:
```

---

## 12. .env File (Secrets)

Create `.env`:

```
MONGO_URI=mongodb://mongo-db:27017/dragonball
JWT_SECRET=your-secret-key-here
ADMIN_USERNAME=admin
ADMIN_PASSWORD=your-secure-password
PORT=4000
```

Add to `.gitignore`:

```
.env
```

---

## 13. Compose Commands

Start:

```
docker compose up -d
```

Stop:

```
docker compose down
```

Stop + delete volumes:

```
docker compose down -v
```

Restart all:

```
docker compose restart
```

Restart one:

```
docker compose restart api
```

Status:

```
docker compose ps
```

Logs:

```
docker compose logs api
```

Follow logs:

```
docker compose logs -f api
```

---

## 14. depends_on Meaning

```
depends_on:
  mongo-db:
    condition: service_healthy
```

* Controls startup order
* Waits until mongo healthcheck passes
* Does NOT replace app retry logic

---

## 15. Best Practice for DB Readiness

Application must retry DB connection internally.

Compose healthcheck is only convenience.

---

## 16. Restart Policies

```
restart: unless-stopped
```

Means:

* Container restarts on crash
* Survives reboot
* Stops only if you stop manually

---

## 17. Persistence Test

```
docker compose down
docker compose up -d
```

Mongo data should remain.

---

## 18. Mental Model

Dockerfile → How to build
Image → Built artifact
Compose → How things run together

---

## 19. Typical Workflow

Write code
→ Dockerfile
→ Build image
→ Push image
→ Compose runs system

---

## 20. Production Reality

Local dev: Docker Compose
Production: Kubernetes

Same concepts, larger scale.

---

END
