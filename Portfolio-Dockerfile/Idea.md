# Docker Quick Reference & Learning Notes

This document summarizes everything covered in our session about Docker, images, containers, Dockerfiles, multi-service containers, supervisord, environment variables, and Docker Hub.

---

## 1. What is Docker

Docker packages an application and its dependencies into an **image**.

From an image, you create a **container** (a running instance).

Think:

```md
Dockerfile → Image → Container
```

---

## 2. Pulling an Image (Download from Registry)

```md
docker pull ubuntu:22.04
```

General form:

```md
docker pull <image_name>:<tag>
```

Example:

```md
docker pull node:22
```

---

## 3. Creating an Image from a Dockerfile

Inside folder containing Dockerfile:

```md
docker build -t myimage:latest .
```

Explanation:

* `-t` → name the image
* `.` → current directory

Directly tag for Docker Hub:

```md
docker build -t username/myimage:latest .
```

---

## 4. Listing Images

```md
docker images
```

---

## 5. Running a Container

```md
docker run myimage
```

With ports:

```md
docker run -p 3000:3000 myimage
```

Detached mode:

```md
docker run -d myimage
```

Named container:

```md
docker run --name mycontainer myimage
```

---

## 6. Listing Containers

Running only:

```md
docker ps
```

All containers:

```md
docker ps -a
```

---

## 7. Stopping a Container

```md
docker stop <container_id>
```

---

## 8. Starting a Stopped Container

```md
docker start <container_id>
```

---

## 9. Removing a Container

```md
docker rm <container_id>
```

Force remove:

```md
docker rm -f <container_id>
```

Remove all containers:

```md
docker rm -f $(docker ps -aq)
```

---

## 10. Removing Images

```md
docker rmi myimage:latest
```

Force:

```md
docker rmi -f myimage:latest
```

Remove all images:

```md
docker rmi -f $(docker images -q)
```

---

## 11. Cleaning Everything

```md
docker system prune -a
```

Removes:

* Images
* Containers
* Cache
* Networks

---

## 12. Tagging Images

Create alias name:

```md
docker tag localimage username/repo:tag
```

Example:

```md
docker tag portfolio shardendumishra22/portfolio:latest
```

Tagging does NOT copy image. It only creates another name.

---

## 13. Docker Hub Login

```md
docker login
```

---

## 14. Pushing Image to Docker Hub

```md
docker push username/repo:latest
```

---

## 15. Pulling Image from Docker Hub

```md
docker pull username/repo:latest
```

---

## 16. ENV in Dockerfile

```md
ENV KEY=value
```

Example:

```md
ENV CI=true
```

Meaning:

All processes inside container see:

```md
KEY=value
```

---

## 17. Why CI=true

* Disables prompts
* Prevents hanging builds
* Forces non-interactive mode

Safe for all builds.

---

## 18. COPY Instruction

```md
COPY . .
```

Copies entire project folder into container.

---

## 19. WORKDIR

```md
WORKDIR /app
```

Equivalent to:

```md
cd /app
```

All following commands run inside this directory.

---

## 20. EXPOSE

```md
EXPOSE 3000
```

Documents that container listens on this port.

Actual publishing is done with:

```md
docker run -p 3000:3000 image
```

---

## 21. CMD

```md
CMD ["command", "arg1"]
```

Defines what runs when container starts.

Only ONE CMD allowed.

---

## 22. One Container = One Foreground Process

Docker allows only ONE foreground process.

If you need multiple servers → must use a **process manager**.

---

## 23. supervisord

Supervisor is a process manager.

It becomes PID 1 and starts many programs.

Docker sees:

```md
supervisord
```

Internally:

* Node server
* Go server
* Frontend
* Next.js

---

## 24. supervisord.conf Example

```md
[supervisord]
nodaemon=true

[program:api]
command=node dist/index.js

autostart=true
autorestart=true
```

Each [program] is a service.

---

## 25. Single Dockerfile with Many Apps (Pattern)

1. Install runtimes
2. Copy code
3. Install deps
4. Build
5. Start supervisor

---

## 26. ENV Files

If project has:

```md
.env
.env.local
```

And Dockerfile contains:

```md
COPY . .
```

Then env files exist inside container automatically.

---

## 27. Port Mapping Rule

```md
container_port → exposed internally
host_port → accessible from browser

-p host:container
```

Example:

```md
docker run -p 8080:3000 image
```

Browser → localhost:8080
Container → 3000

---

## 28. Image vs Container vs Cache

Image = blueprint
Container = running instance
Cache = build layers

Deleting one does NOT delete others.

---

## 29. Recommended Build Flow

```md
docker build -t username/app:latest .
docker push username/app:latest
```

---

## 30. Recommended Run Flow

```md
docker pull username/app:latest
docker run -d -p 3000:3000 username/app:latest
```

---

If you want next-level docs later:

* Multi-stage builds
* docker-compose
* Volume mounts
* Healthchecks
* Reverse proxy

We can extend this file later.
