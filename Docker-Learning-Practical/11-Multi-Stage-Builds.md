# Multi-Stage Docker Builds

## Overview

Multi-stage builds allow you to create smaller, more efficient Docker images by using multiple FROM statements in a single Dockerfile.

## Benefits

- **Smaller Images**: Only the final stage is used in the final image
- **Better Security**: Build tools and dependencies aren't included in the final image
- **Simplified Dockerfile**: No need for separate build and runtime Dockerfiles
- **Faster Builds**: Intermediate stages can be cached

## Basic Example

```dockerfile
# Stage 1: Build
FROM node:18 AS builder
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

# Stage 2: Production
FROM node:18-alpine
WORKDIR /app
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules ./node_modules
EXPOSE 3000
CMD ["node", "dist/index.js"]
```

## Go Example

```dockerfile
# Build stage
FROM golang:1.21 AS builder
WORKDIR /app
COPY go.mod go.sum ./
RUN go mod download
COPY . .
RUN CGO_ENABLED=0 GOOS=linux go build -o main .

# Production stage
FROM alpine:latest
RUN apk --no-cache add ca-certificates
WORKDIR /root/
COPY --from=builder /app/main .
EXPOSE 8080
CMD ["./main"]
```

## Python Example

```dockerfile
# Build stage
FROM python:3.11 AS builder
WORKDIR /app
COPY requirements.txt .
RUN pip install --user -r requirements.txt

# Production stage
FROM python:3.11-slim
WORKDIR /app
COPY --from=builder /root/.local /root/.local
COPY . .
ENV PATH=/root/.local/bin:$PATH
CMD ["python", "app.py"]
```

## Advanced Techniques

### Named Stages

```dockerfile
FROM node:18 AS dependencies
# Install dependencies

FROM node:18 AS build
# Build application

FROM node:18 AS test
# Run tests

FROM nginx:alpine AS production
# Production image
```

### Copying from Multiple Stages

```dockerfile
FROM builder1 AS stage1
# First build

FROM builder2 AS stage2
# Second build

FROM base
COPY --from=stage1 /app/output1 /app/
COPY --from=stage2 /app/output2 /app/
```

## Best Practices

- Use official slim or alpine base images for the final stage
- Order stages from least to most frequently changing
- Leverage build cache by copying dependency files before source code
- Use `.dockerignore` to exclude unnecessary files
- Name stages for clarity when copying artifacts
- Consider using a specific stage for testing
