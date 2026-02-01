# DevOps Learning Course - Complete Guide

---

## Table of Contents

- [Linux Essentials](#linux-essentials)
- [Linux Advanced](#linux-advanced)
- [Git Fundamentals](#git-fundamentals)
- [Git Advanced](#git-advanced)
- [Docker Fundamentals](#docker-fundamentals)
- [Docker Advanced](#docker-advanced)
- [Jenkins Fundamentals](#jenkins-fundamentals)
- [Jenkins Advanced](#jenkins-advanced)
- [Kubernetes Fundamentals](#kubernetes-fundamentals)
- [Kubernetes Advanced](#kubernetes-advanced)
- [Ansible](#ansible)
- [Terraform Fundamentals](#terraform-fundamentals)
- [Terraform Advanced](#terraform-advanced)
- [AWS Cloud Computing](#aws-cloud-computing)
- [Monitoring Tools](#monitoring-tools)

---

## Linux Essentials

## Module 1: Foundations of Linux and Operating Systems

### 1. What is an Operating System?

- OS as an abstraction layer between applications and hardware

**Tasks of an OS:**

- Resource Allocation and Management
- File Management
- Device Management
- Security
- Networking
- Multi-user and Multitasking in Linux

### 2. Components of an OS

| Component | Description |
|-----------|-------------|
| **Kernel** | The core of Linux |
| **Application Layer** | Interaction through GUIs or CLIs |
| **System Calls** | How to interact with the kernel |

### 3. Introduction to the Big 3 Operating Systems

- Comparison of Windows, macOS, and Linux
- Client OS vs. Server OS
- Why Linux is preferred in DevOps

---

## Module 2: Virtualization and Linux VMs

### 1. Understanding Virtualization and Virtual Machines

- What is virtualization? (Example: Running multiple "computers" on one physical machine)
- Architecture: Virtualization layers and hypervisors

### 2. Hypervisors

| Type | Description | Examples |
|------|-------------|----------|
| **Type 1** | Bare-Metal Hypervisors | VMware ESXi, KVM |
| **Type 2** | Hosted Hypervisors | VirtualBox, VMware Workstation |

### 3. Setting up a Linux VM

**Hands-on:**

- Installing VirtualBox and creating an Ubuntu VM
- Exploring VM settings: Memory, CPUs, and storage
- Installing Linux using ISO files

---

## Module 3: Exploring the Linux File System

### 1. Linux File System Basics

- Everything in Linux is a file: Explanation and examples
- File types: Regular files, directories, and binaries
- Hidden files: `.filename` convention

### 2. Understanding the Linux Directory Structure

| Directory | Purpose |
|-----------|---------|
| `/home` | User home directories |
| `/etc` | Configuration files |
| `/var` | Variable data (logs, etc.) |
| `/bin` | Essential binaries |
| `/tmp` | Temporary files |

**Navigation Commands:**

```bash
pwd     # Print working directory
ls      # List directory contents
cd      # Change directory
```

- File permissions and ownership: Basics and `umask`

### 3. GUI vs. CLI in Linux

- Advantages of using CLI in DevOps workflows

---

## Module 4: Managing Software on Linux

### 1. What is a Package Manager?

- Purpose and tasks of a package manager
- Understanding software packages and repositories
- Pre-installed package managers (e.g., APT, YUM)

### 2. Hands-On with Package Managers

**Basic commands:**

```bash
# Debian/Ubuntu (APT)
apt update
apt install <package>
apt remove <package>

# RHEL/CentOS (YUM)
yum update
yum install <package>
yum remove <package>
```

- Adding repositories and using PPAs (Personal Package Archives)

### 3. Alternative Ways to Install Software

- Binary downloads
- Source code compilation

---

## Module 5: Working with Text Editors

### 1. Introduction to Nano, Vi, and Vim

- What is Vim?
- Differences and use cases for CLI-based text editors in Linux

### 2. Vim Basics

| Mode | Description |
|------|-------------|
| **Insert Mode** | For typing/editing text |
| **Command Mode** | For executing commands |

**Essential Commands:**

```vim
i       # Enter insert mode
Esc     # Return to command mode
:w      # Save file
:q      # Quit
:wq     # Save and quit
:q!     # Quit without saving
```

### 3. Nano Basics

- Basic commands and modes for Nano

---

## Module 6: Linux User and Permission Management

### 1. Understanding User Accounts

| Account Type | Description |
|--------------|-------------|
| **User** | Regular user accounts |
| **Superuser** | Root/admin accounts |
| **Service** | System service accounts |

- `/etc/passwd` file overview
- UID: User Identifier

### 2. Managing Users and Groups

**High-level utilities:**

```bash
adduser <username>
addgroup <groupname>
```

**Low-level utilities:**

```bash
useradd <username>
groupadd <groupname>
```

- Sudoers configuration: Managing privileged access

### 3. Understanding Permissions

**File ownership:** User, Group, Others

**Permission types:**

| Symbol | Permission |
|--------|------------|
| `r` | Read |
| `w` | Write |
| `x` | Execute |

**Changing permissions:**

```bash
# Symbolic method
chmod u+x file.sh

# Numeric method
chmod 755 file.sh
```

---

# Linux Advanced

## Module 7: Mastering the Command Line

### 1. Essential Commands for File and Directory Management

**File operations:**

```bash
touch file.txt      # Create empty file
rm file.txt         # Remove file
mv old.txt new.txt  # Move/rename file
cp src.txt dest.txt # Copy file
```

**Directory operations:**

```bash
mkdir dirname       # Create directory
rmdir dirname       # Remove empty directory
rm -rf dirname      # Remove directory recursively
```

### 2. Pipes and Redirects

```bash
# Pipe: Chain commands
command1 | command2

# Redirect output
command > file.txt   # Overwrite
command >> file.txt  # Append
```

### 3. Practical Examples

- Combining commands for log analysis with `grep` and `less`
- Using wildcards and subshells for advanced use cases

---

## Module 8: Introduction to Shell Scripting

### 1. Why Shell Scripting?

- Automation use cases in DevOps
- Writing and executing shell scripts

### 2. Key Shell Scripting Concepts

```bash
#!/bin/bash

# Variables
NAME="DevOps"

# Conditionals
if [ "$NAME" == "DevOps" ]; then
    echo "Learning $NAME"
fi

# Loops
for i in 1 2 3; do
    echo "Number: $i"
done

while [ $count -lt 5 ]; do
    echo $count
    ((count++))
done
```

- Built-ins, boolean operators, and loops (`for`, `while`)
- File test operators and relational operators
- Passing arguments using positional parameters (`$1`, `$2`, etc.)

### 3. Advanced Shell Scripting

- Functions
- Scheduling scripts with `cron`
- Hands-on projects: Automated backups and log cleanup
- Debugging scripts: `bash -x`, `set -x`

---

## Module 9: Environment Variables

### 1. Understanding Environment Variables

- What are they? (Analogy: Environment-specific configurations)
- System-wide vs. user-specific variables

### 2. Working with Environment Variables

```bash
# Set variable
export MY_VAR="value"

# View variable
echo $MY_VAR

# Persist in ~/.bashrc or ~/.profile
```

- Real-world use cases in application deployment

---

## Module 10: Networking Essentials

### 1. Introduction to Networking

| Network Type | Description |
|--------------|-------------|
| **LAN** | Local Area Network |
| **WAN** | Wide Area Network |
| **MAN** | Metropolitan Area Network |
| **PAN** | Personal Area Network |

- Key components: Switches, routers, gateways

### 2. IP Addressing

- IP, subnet masks, and gateways
- CIDR notation and subnets

### 3. Essential Networking Commands for DevOps

```bash
ping <host>         # Test connectivity
ifconfig            # Network interface config
netstat             # Network statistics
traceroute <host>   # Trace packet route
```

- Basics of firewalls (`iptables` or `ufw`)

---

## Module 11: Secure Shell (SSH)

### 1. What is SSH?

- Secure remote communication between computers
- Use cases in DevOps workflows

### 2. How SSH Works

**Authentication methods:**

- Username/password
- SSH key pairs (recommended)

```bash
# Generate SSH key
ssh-keygen -t rsa -b 4096 -C "your.email@example.com"

# Copy public key to server
ssh-copy-id user@server
```

### 3. Practical Applications of SSH

- Connecting to remote servers
- Using SSH in automation workflows
- Managing servers using `~/.ssh/config`

---

# Git Fundamentals

## Module 1: Introduction to Version Control

### What is Version Control?

Version control tracks and manages changes to a project over time, allowing multiple people to work on the same project without interfering with each other's work.

| Tool | Description |
|------|-------------|
| **Git** | A tool that helps track and manage your code's history |
| **GitHub** | A cloud service where you can store and share your Git repositories |

---

## Module 2: Basic Concepts of Git

| Concept | Description |
|---------|-------------|
| **Repository** | A place where all your project files and their history are stored |
| **Commit** | A snapshot of changes made to files in the repository |
| **Branch** | A separate line of development in the repository |
| **HEAD** | A pointer to the current commit or branch you're working on |

---

## Module 3: Setup Git Repository

### Installing Git

Get Git from [git-scm.com](https://git-scm.com)

### Configure Git

```bash
git config --global user.name "Name"
git config --global user.email "abc@abc.com"
```

---

## Module 4: Working with Git

### Initialize Git Locally

```bash
git init  # Initializes a new Git repository in the current directory
```

### Clone a Repository

```bash
git clone <repository-url>
```

---

## Module 5: Git Branching and Workflow

### Branch Operations

```bash
# Create a branch
git branch <branch-name>

# Switch to a branch
git checkout <branch-name>

# Create and switch in one command
git checkout -b <branch-name>

# Delete a local branch
git branch -d <branch-name>

# Delete a remote branch
git push origin --delete <branch-name>
```

---

## Module 6: Git Merge and Rebase

### Git Merge

Combines changes from different branches and creates a new merge commit.

```bash
git checkout main
git merge feature-xyz
```

### Git Rebase

Rewrites the history by applying your commits on top of another branch.

```bash
git fetch origin
git rebase origin/main
```

---

## Module 7: Undoing Changes with Git

### Git Restore

```bash
# Discard changes in working directory
git restore <file-name>

# Unstage a file
git restore --staged <file-name>
```

### Git Reset

```bash
# Soft reset (keeps changes staged)
git reset --soft <commit-hash>

# Mixed reset (keeps changes unstaged)
git reset --mixed <commit-hash>

# Hard reset (discards all changes)
git reset --hard <commit-hash>
```

### Git Revert

```bash
git revert <commit-hash>  # Creates a new commit to undo changes
```

### Git Commit Amend

```bash
git commit --amend  # Modify the last commit
```

---

# Git Advanced

## Module 8: Resolving Merge Conflicts

### What is a Merge Conflict?

A merge conflict happens when Git can't automatically merge changes because both branches modified the same part of a file.

### How to Resolve

1. Identify the conflict (markers: `<<<<<<<`, `=======`, `>>>>>>>`)
2. Manually edit the file to resolve the conflict
3. Stage the resolved file:

   ```bash
   git add <file-name>
   ```

4. Complete the merge:

   ```bash
   git merge --continue
   ```

### Avoiding Merge Conflicts

```bash
git pull origin main  # Frequently pull changes
```

---

## Module 9: Working with SSH Keys for Authentication

### Generate SSH Key Pair

```bash
# Generate key
ssh-keygen -t rsa -b 4096 -C "your.email@example.com"

# Display public key
cat ~/.ssh/id_rsa.pub

# Test connection
ssh -T git@github.com
```

---

## Module 10: Git Client (GUI or Command Line Tool)

### Git GUI Tools

| Tool | Description |
|------|-------------|
| **GitHub Desktop** | Simple GUI for managing repositories |
| **SourceTree** | Powerful Git GUI with Git and Mercurial support |
| **GitKraken** | Modern Git client with beautiful interface |

---

## Module 11: Additional Advanced Git Commands

```bash
# Cherry-pick: Apply specific commit
git cherry-pick <commit-hash>

# Diff: Show differences
git diff <commit1> <commit2>

# Log: View commit history
git log
git log --oneline

# Tag: Mark specific commits
git tag <tag-name>
```

---

## Module 12: Git Stash and Git Pop

```bash
# Save changes temporarily
git stash
git stash list

# Retrieve stashed changes
git stash pop
git stash apply <stash-name>
```

---

## Module 13: Advanced Merging Strategies (Squash Merge)

```bash
git merge --squash <branch-name>
git commit
```

---

## Module 14: Git Hooks (Advanced)

Automating tasks with Git Hooks (e.g., pre-commit, pre-push)

```bash
# Example: Pre-push hook to run tests
git commit --pre-commit-hook
```

---

## Module 15: Rebasing and Interactive Rebasing

```bash
# Interactive rebase last 3 commits
git rebase -i HEAD~3
```

---

# Docker Fundamentals

## Module 1: Introduction to Build and Package Manager Tools

### 1.1 What are Build and Package Manager Tools?

- Overview of build and package manager tools in DevOps
- Importance of automating build processes
- Examples: Maven, Gradle, npm, pip, etc.

### 1.2 What is an "Artifact"?

- Definition and types of artifacts (JAR, WAR, Docker images, etc.)
- Role of artifacts in deployment and distribution

### 1.3 What Does "Building the Code" Mean?

- Definition of the build process (compiling, packaging, testing)
- Continuous Integration (CI) and Continuous Deployment (CD) in DevOps

### 1.4 What is an "Artifact Repository"?

- Introduction to artifact repositories (e.g., Nexus, Artifactory)
- Managing and storing versioned artifacts
- Benefits of centralized artifact storage

### 1.5 Different Build Tools for Different Programming Languages

| Language | Build Tools |
|----------|-------------|
| Java | Maven, Gradle |
| JavaScript | npm, Yarn, Webpack |
| Python | pip, virtualenv |
| C/C++ | Conan |
| C# | NuGet |
| Golang | dep |
| Ruby | RubyGems |

---

## Module 2: Introduction to Docker and Containers

### 2.1 What is Docker?

- Overview of Docker as a containerization platform
- Docker's role in modern software development and DevOps

### 2.2 What is a Container?

- Definition and key differences between containers and traditional virtual machines
- Containerization benefits: scalability, portability, and efficiency

### 2.3 Container vs Image

| Concept | Description |
|---------|-------------|
| **Image** | Immutable snapshot/template |
| **Container** | Running instance of an image |

### 2.4 Docker vs Virtual Machines

| Feature | Docker | VM |
|---------|--------|-----|
| Boot Time | Seconds | Minutes |
| Size | MBs | GBs |
| Isolation | Process level | Full OS |
| Performance | Near native | Overhead |

### 2.5 Most Popular Container Technologies

- Docker
- Containerd
- CRI-O

### 2.6 Docker Architecture & Components

| Component | Description |
|-----------|-------------|
| **Docker Daemon** | Manages containers and images |
| **Docker Client** | Interacts with Docker Daemon |
| **Docker Registry** | Stores Docker images |
| **Docker Images** | Immutable snapshots |
| **Docker Containers** | Running instances |
| **Dockerfile** | Build instructions |

### 2.7 Docker Installation and Setup

- Installing Docker on Linux, Windows, and macOS
- Introduction to Docker Desktop for Windows and macOS

---

## Module 3: Docker Commands and Usage

### 3.1 Main Docker Commands

```bash
docker run <image>      # Create and start container
docker pull <image>     # Pull image from registry
docker start <container> # Start a container
docker stop <container>  # Stop a container
docker ps               # View running containers
docker ps -a            # Show all containers
docker images           # List available images
```

### 3.2 Debug Commands

```bash
docker logs <container>          # Fetch container logs
docker exec -it <container> bash # Start bash session
docker inspect <container>       # View detailed info
```

### 3.3 Docker Ports

- Mapping container ports to host ports
- Exposing ports for communication

```bash
docker run -p 8080:80 nginx  # Map host:container ports
```

---

## Module 4: Dockerfile and Docker Image Management

### 4.1 Dockerfile Basics

```dockerfile
FROM ubuntu:20.04
COPY . /app
ENV APP_ENV=production
WORKDIR /app
RUN apt-get update && apt-get install -y python3
ENTRYPOINT ["python3"]
CMD ["app.py"]
```

| Instruction | Description |
|-------------|-------------|
| `FROM` | Specify base image |
| `COPY` | Copy files to container |
| `ENV` | Set environment variables |
| `ENTRYPOINT` | Default executable |
| `RUN` | Execute commands during build |
| `CMD` | Default command at runtime |

### 4.2 Multi-Stage Builds

```dockerfile
# Build stage
FROM node:16 AS builder
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

# Production stage
FROM nginx:alpine
COPY --from=builder /app/dist /usr/share/nginx/html
```

### 4.3 Distroless Images

- What are distroless images and when to use them
- Benefits for security and reduced attack surface

### 4.4 Tagging and Pushing Docker Images

```bash
docker tag myapp:latest myregistry/myapp:v1.0
docker push myregistry/myapp:v1.0
```

---

# Docker Advanced

## Module 5: Docker Compose and Multi-Container Applications

### 5.1 What is Docker Compose?

Overview of Docker Compose for managing multi-container applications

### 5.2 Docker Compose File

```yaml
version: '3.8'
services:
  web:
    build: .
    ports:
      - "8080:80"
    depends_on:
      - db
  db:
    image: postgres:13
    environment:
      POSTGRES_PASSWORD: secret
    volumes:
      - db-data:/var/lib/postgresql/data

volumes:
  db-data:
```

### 5.3 Managing Containers with Docker Compose

```bash
docker-compose up -d     # Start services
docker-compose down      # Stop services
docker-compose logs      # View logs
docker-compose scale web=3  # Scale service
```

---

## Module 6: Docker Volumes and Data Persistence

### 6.1 Why Persistence is Important

| Volume Type | Description |
|-------------|-------------|
| **Host Volumes** | Map host directory |
| **Anonymous Volumes** | Docker-managed, unnamed |
| **Named Volumes** | Docker-managed, named |

### 6.2 Working with Volumes

```bash
# Create named volume
docker volume create mydata

# Run with volume
docker run -v mydata:/app/data myimage

# List volumes
docker volume ls
```

---

## Module 7: Docker Networking Deep Dive

### 7.1 Bridge Network (Default)

- Use Case: Connecting containers on the same host

### 7.2 Host Network

- Use Case: Removing isolation between container and host

### 7.3 IPvlan and Macvlan Networks

| Network | Use Case |
|---------|----------|
| **IPvlan** | Custom IP management |
| **Macvlan** | Direct network access with MAC addresses |

```bash
# Create custom network
docker network create mynetwork

# Run container on network
docker run --network mynetwork myimage
```

---

## Module 8: Advanced Docker Concepts

### 8.1 Private Docker Registry

- Setting up with AWS or Nexus
- Pushing and pulling images from private repositories

### 8.2 CI/CD Integration with Docker

- Using Docker in CI/CD pipelines
- Automating build, test, and deployment

### 8.3 Docker with Nexus Repository

- Running Nexus as a Docker container
- Pushing/pulling Docker images to/from Nexus

---

## Module 9: Final Project

### 9.1 Demo Project Overview

- Setting up a complete DevOps pipeline using Docker
- Building, testing, and deploying multi-container applications
- Pushing Docker images to private registry

### 9.2 Hands-On Project Development

- Developing containerized JavaScript or Java application
- Using Docker volumes for data persistence
- Automating with Docker Compose and CI/CD tools

---

# Jenkins Fundamentals

## Module 1: Introduction to Build Automation and CI/CD

### What is Build Automation?

- Automating source code retrieval
- Executing automated tests
- Compiling code/building Docker images
- Pushing artifacts to repositories
- Deploying artifacts to environments

### What is CI/CD?

| Concept | Description |
|---------|-------------|
| **Continuous Integration** | Merging, testing, and building code changes in real-time |
| **Continuous Deployment** | Automatically deploying validated builds |

### Comparing Build Automation Tools

- Jenkins
- GitLab CI
- Travis CI
- Bamboo
- TeamCity

---

## Module 2: Getting Started with Jenkins

### Introduction to Jenkins

- Overview of Jenkins
- Jenkins' role in DevOps pipelines
- Key features and integrations

### Jenkins Architecture

- Master-agent model
- How Jenkins manages tasks and workloads

### Installing Jenkins

```bash
# Docker
docker run -p 8080:8080 jenkins/jenkins:lts

# Or install directly on system
```

### Jenkins User Roles

| Role | Responsibilities |
|------|------------------|
| **Administrator** | Plugin management, setup |
| **User** | Job creation, pipeline management |

---

## Module 4: Advanced Pipeline Configurations

### Pipeline as Code

- Storing Jenkinsfiles in version control
- Declarative vs Scripted pipelines

```groovy
// Declarative Pipeline
pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }
        stage('Deploy') {
            steps {
                sh './deploy.sh'
            }
        }
    }
}
```

### Using Shared Libraries

- Creating and managing shared libraries
- Integrating external Groovy scripts
- Reusing code across pipelines

---

## Module 5: Integrating Jenkins with DevOps Tools

### SCM Integration

- Configuring GitHub, GitLab, Bitbucket
- Setting up Webhooks for automatic triggers

### Build Tool Integration

- Configuring Maven, Gradle, npm, and Docker

### Test Automation Integration

- Configuring with Selenium, JUnit, and TestNG
- Managing test reports and logs

---

# Jenkins Advanced

## Module 6: Credentials and Security Management

### Managing Credentials in Jenkins

| Scope | Description |
|-------|-------------|
| **System** | Available to Jenkins and agents |
| **Global** | Available to all jobs |

**Credential Types:**

- Secret text
- Username/password
- SSH keys
- Docker host certificates

### Securing Jenkins

- Role-based access control
- Securing Jenkinsfile parameters
- Backup and restore strategies

---

## Module 7: Monitoring and Optimizing Jenkins

### Monitoring Performance

- Using plugins for performance monitoring
- Tracking build durations and resource usage

### Optimizing Jobs

- Leveraging distributed builds
- Managing workspace and build history

---

## Module 8: Versioning and Best Practices

### Software Versioning

- Semantic versioning (major.minor.patch)
- Automating dynamic versioning

### Best Practices

- ✅ Use pipeline as code
- ✅ Store Jenkinsfiles in repositories
- ✅ Create modular, reusable scripts
- ✅ Use shared libraries effectively
- ✅ Regularly update Jenkins and plugins

---

## Module 9: Real-World Use Cases

### End-to-End CI/CD Pipeline

- Building multi-stage pipeline for Java application
- Containerizing with Docker
- Deploying to Kubernetes

### Troubleshooting Jenkins

- Common pipeline errors and solutions
- Debugging failed builds and logs

---

## Module 10: Emerging Trends

### Scaling Jenkins

- Setting up Jenkins in Kubernetes cluster
- Using Jenkins X for cloud-native CI/CD

### Jenkins Alternatives

- Exploring modern alternatives
- When to choose Jenkins over other tools

---

# Kubernetes Fundamentals

## Module 1: Introduction to Containers and Kubernetes

### 1. Understanding Monoliths and Microservices

| Architecture | Description |
|--------------|-------------|
| **Monolith** | Single, unified application |
| **Microservices** | Distributed, independent services |

### 2. The Evolution from VMs to Containers

- Differences between VMs and Containers
- Why containers became a game-changer

### 3. Container Fundamentals

- Containers as Linux processes
- Linux namespaces (isolated processes)
- Linux cgroups

### 4. Introduction to Kubernetes

- What is Kubernetes?
- Why Kubernetes? The need for orchestration
- Core features of Kubernetes

---

## Module 2: Kubernetes Architecture and Components

### 1. Core Components

**Control Plane:**

- API Server
- Scheduler
- Controller Manager
- etcd

**Node Components:**

- kubelet
- kube-proxy
- Container runtime

**Addons:**

- DNS
- CNI plugins

### 2. Kubeconfig File

```yaml
apiVersion: v1
kind: Config
clusters:
- cluster:
    server: https://kubernetes.local:6443
  name: my-cluster
contexts:
- context:
    cluster: my-cluster
    user: my-user
  name: my-context
current-context: my-context
users:
- name: my-user
  user:
    token: <token>
```

### 3. Interacting with Kubernetes API

- GVR (Group, Version, Resource) and GVK (Group, Version, Kind)
- Using `curl` to query Kubernetes API
- Using `kubectl proxy`

---

## Module 3: Workloads and YAML Basics

### 1. Declarative vs Imperative

| Approach | Command |
|----------|---------|
| **Imperative** | `kubectl run nginx --image=nginx` |
| **Declarative** | `kubectl apply -f nginx.yaml` |

### 2. YAML Basics

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
  labels:
    app: myapp
spec:
  containers:
  - name: nginx
    image: nginx:latest
    ports:
    - containerPort: 80
```

### 3. Pods

- What is a Pod?
- Init containers
- Sidecar containers
- Pod lifecycle and phases

---

## Module 4: Advanced Pod Concepts

### 1. Pod Disruption Budgets

```yaml
apiVersion: policy/v1
kind: PodDisruptionBudget
metadata:
  name: my-pdb
spec:
  minAvailable: 2
  selector:
    matchLabels:
      app: myapp
```

### 2. Resource Requests and Limits

```yaml
resources:
  requests:
    memory: "64Mi"
    cpu: "250m"
  limits:
    memory: "128Mi"
    cpu: "500m"
```

### 3. Quality of Service (QoS) Tiers

| QoS Class | Description |
|-----------|-------------|
| **Guaranteed** | Requests = Limits |
| **Burstable** | Requests < Limits |
| **BestEffort** | No requests/limits |

---

## Module 5: Kubernetes Scheduling

### 1. Namespaces

```bash
kubectl create namespace dev
kubectl get pods -n dev
```

### 2. Labels and Selectors

```yaml
metadata:
  labels:
    app: myapp
    tier: frontend
```

### 3. Node Affinity

```yaml
affinity:
  nodeAffinity:
    requiredDuringSchedulingIgnoredDuringExecution:
      nodeSelectorTerms:
      - matchExpressions:
        - key: disktype
          operator: In
          values:
          - ssd
```

### 4. Taints and Tolerations

```bash
# Add taint
kubectl taint nodes node1 key=value:NoSchedule

# Toleration in pod spec
tolerations:
- key: "key"
  operator: "Equal"
  value: "value"
  effect: "NoSchedule"
```

---

# Kubernetes Advanced

## Module 6: ReplicaSets, Deployments, and Probes

### 1. ReplicaSet

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: my-replicaset
spec:
  replicas: 3
  selector:
    matchLabels:
      app: myapp
  template:
    metadata:
      labels:
        app: myapp
    spec:
      containers:
      - name: nginx
        image: nginx
```

### 2. Deployments

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-deployment
spec:
  replicas: 3
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 1
  selector:
    matchLabels:
      app: myapp
  template:
    # ... pod template
```

**Deployment Strategies:**

- Recreate
- Rolling Update
- Canary

### 3. Kubernetes Probes

```yaml
livenessProbe:
  httpGet:
    path: /health
    port: 8080
  initialDelaySeconds: 3
  periodSeconds: 10

readinessProbe:
  httpGet:
    path: /ready
    port: 8080
  initialDelaySeconds: 5
  periodSeconds: 5

startupProbe:
  httpGet:
    path: /startup
    port: 8080
  failureThreshold: 30
  periodSeconds: 10
```

---

## Module 7: ConfigMaps and Secrets

### 1. ConfigMaps

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: my-config
data:
  APP_ENV: production
  APP_DEBUG: "false"
```

**Usage:**

```yaml
# As environment variable
envFrom:
- configMapRef:
    name: my-config

# As volume
volumes:
- name: config-volume
  configMap:
    name: my-config
```

### 2. Secrets

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: my-secret
type: Opaque
data:
  password: cGFzc3dvcmQ=  # base64 encoded
```

---

## Module 8: StatefulSets, Services, and Ingress

### 1. StatefulSets

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: my-statefulset
spec:
  serviceName: "my-service"
  replicas: 3
  selector:
    matchLabels:
      app: myapp
  template:
    # ... pod template
```

### 2. Services

| Type | Description |
|------|-------------|
| **ClusterIP** | Internal cluster access |
| **NodePort** | External access via node port |
| **LoadBalancer** | External load balancer |
| **ExternalName** | DNS CNAME record |

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  type: ClusterIP
  selector:
    app: myapp
  ports:
  - port: 80
    targetPort: 8080
```

### 3. Ingress

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: my-ingress
spec:
  rules:
  - host: myapp.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: my-service
            port:
              number: 80
```

---

## Module 9: Authentication, Authorization, and Admission

### 1. RBAC (Role-Based Access Control)

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: pod-reader
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "watch", "list"]
---
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: read-pods
subjects:
- kind: User
  name: jane
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```

### 2. Admission Controllers

- Validating admission webhooks
- Mutating admission webhooks

---

## Module 10: Kubernetes Volumes

### 1. Volume Types

| Type | Description |
|------|-------------|
| `emptyDir` | Temporary storage |
| `hostPath` | Host filesystem |
| `local` | Local storage |
| `nfs` | Network File System |

### 2. Persistent Volumes (PV) and Claims (PVC)

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: my-pv
spec:
  capacity:
    storage: 10Gi
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  hostPath:
    path: /data
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi
```

---

## Module 11: Project Deployment

### End-to-End Deployment

- Python Cloud-Native App deployment
- PostgreSQL via CloudNativePG
- Ingress and Cert-Manager integration for HTTPS
- Adding DNS records for production readiness

---

# Ansible

## Module 1: Introduction to Ansible

### Overview of Ansible

- Open-source origins: History, vision, and evolution
- Challenges it addresses in configuration management
- Comparison with Puppet, Chef, SaltStack

### Core Features

- ✅ Agentless architecture
- ✅ Declarative approach
- ✅ YAML-based configuration
- ✅ SSH as transport mechanism

### Industry Use Cases

- Managing configurations across multiple servers
- Orchestrating application deployments
- Automating cloud provisioning

---

## Module 2: Getting Started with Ansible

### Installing Ansible

```bash
# Linux (apt)
sudo apt update
sudo apt install ansible

# macOS
brew install ansible

# pip
pip install ansible
```

### Ansible Configuration Basics

**ansible.cfg:**

```ini
[defaults]
inventory = ./inventory
remote_user = ubuntu
private_key_file = ~/.ssh/id_rsa
```

**Inventory file:**

```ini
[webservers]
web1.example.com
web2.example.com

[databases]
db1.example.com
```

---

## Module 3: Ansible Architecture and Workflow

### Core Components

| Component | Description |
|-----------|-------------|
| **Control Node** | Machine where Ansible runs |
| **Managed Nodes** | Target machines |
| **Modules** | Units of work |
| **Plugins** | Extend Ansible functionality |
| **Inventory** | List of managed nodes |

### Workflow

```
Write -> Test -> Execute
```

- Idempotency in Ansible operations

---

## Module 4: Ansible Ad-Hoc Commands

```bash
# Ping all hosts
ansible all -m ping

# File management
ansible webservers -m file -a "path=/tmp/test state=directory"

# Service control
ansible webservers -m service -a "name=nginx state=started"

# User management
ansible all -m user -a "name=deploy state=present"
```

---

## Module 5: Ansible Playbooks

### Anatomy of a Playbook

```yaml
---
- name: Configure web servers
  hosts: webservers
  become: yes
  vars:
    http_port: 80
  
  tasks:
    - name: Install nginx
      apt:
        name: nginx
        state: present
      tags: install

    - name: Start nginx
      service:
        name: nginx
        state: started
        enabled: yes
      notify: Restart nginx

  handlers:
    - name: Restart nginx
      service:
        name: nginx
        state: restarted
```

---

## Module 6: Ansible Modules

### Core Modules

| Module | Purpose |
|--------|---------|
| `file` | Manage files and directories |
| `user` | Manage user accounts |
| `service` | Manage services |
| `package` | Install packages |

### Cloud Modules

- AWS modules
- Azure modules
- GCP modules

---

## Module 7: Variables in Ansible

### Types of Variables

```yaml
# In playbook
vars:
  http_port: 80

# Host variables (host_vars/hostname.yml)
nginx_port: 8080

# Group variables (group_vars/webservers.yml)
app_env: production

# Registered variables
- name: Get uptime
  command: uptime
  register: uptime_result
```

### Ansible Vault

```bash
# Create encrypted file
ansible-vault create secrets.yml

# Edit encrypted file
ansible-vault edit secrets.yml

# Run playbook with vault
ansible-playbook site.yml --ask-vault-pass
```

---

## Module 8: Advanced Templating with Jinja2

```jinja2
# nginx.conf.j2
server {
    listen {{ http_port }};
    server_name {{ server_name }};
    
    {% if ssl_enabled %}
    ssl_certificate {{ ssl_cert_path }};
    {% endif %}
    
    {% for location in locations %}
    location {{ location.path }} {
        proxy_pass {{ location.backend }};
    }
    {% endfor %}
}
```

---

## Module 9: Roles in Ansible

### Role Structure

```
roles/
└── webserver/
    ├── tasks/
    │   └── main.yml
    ├── handlers/
    │   └── main.yml
    ├── templates/
    │   └── nginx.conf.j2
    ├── files/
    ├── vars/
    │   └── main.yml
    ├── defaults/
    │   └── main.yml
    └── meta/
        └── main.yml
```

### Using Roles

```yaml
- hosts: webservers
  roles:
    - webserver
    - { role: database, when: "db_enabled" }
```

---

## Module 10: Ansible Galaxy and Collections

```bash
# Install role from Galaxy
ansible-galaxy install geerlingguy.nginx

# Install collection
ansible-galaxy collection install amazon.aws

# List installed roles
ansible-galaxy list
```

---

## Module 11: Ansible for Cloud Automation

- Provisioning in AWS, Azure, and GCP
- Writing playbooks for cloud infrastructure
- Integrating with Terraform for hybrid environments

---

## Module 12: Ansible Tower / AWX

### Features

- Role-based access control (RBAC)
- Job scheduling and workflows
- Visualizing playbook execution

### Installing AWX (Docker-based)

```bash
docker-compose up -d
```

---

## Module 13: Security and Compliance

### Securing Ansible Operations

- Using Ansible Vault to encrypt sensitive data
- Role-based access and SSH key management

### Compliance Automation

- Writing compliance playbooks
- Auditing and remediation with Ansible

---

# Terraform Fundamentals

## Module 1: Introduction to Terraform

### What is Terraform?

- Definition, origin, and evolution
- Key features: declarative vs imperative approach
- Comparison with CloudFormation, Ansible

### Open Source and Beyond

| Edition | Features |
|---------|----------|
| **Terraform OSS** | Free, open-source |
| **Terraform Cloud** | Collaboration features |
| **Terraform Enterprise** | Enterprise features |

### Infrastructure as Code (IaC)

- Declarative vs Imperative IaC
- Why IaC is essential in DevOps

---

## Module 2: Terraform Fundamentals

### Core Concepts

| Concept | Description |
|---------|-------------|
| **Providers** | Interface to cloud platforms |
| **Resources** | Infrastructure components |
| **State Files** | Track infrastructure state |

### Terraform Workflow

```
Write -> Plan -> Apply
```

---

## Module 3: Terraform Architecture

### Terraform Core

- Role in interacting with providers
- Execution plans and dependency graph

### Providers

```hcl
provider "aws" {
  region = "us-west-2"
}

provider "google" {
  project = "my-project"
  region  = "us-central1"
}
```

### State Management

| Type | Description |
|------|-------------|
| **Local State** | Stored on local machine |
| **Remote State** | Stored in cloud (S3, etc.) |

---

## Module 4: Terraform CLI and Commands

### Core Commands

```bash
terraform init      # Initialize working directory
terraform plan      # Preview changes
terraform apply     # Apply changes
terraform destroy   # Destroy infrastructure
terraform validate  # Validate configuration
terraform fmt       # Format configuration
terraform refresh   # Update state
```

### Advanced Commands

```bash
terraform taint <resource>   # Mark resource for recreation
terraform import <resource>  # Import existing infrastructure
terraform graph              # Generate dependency graph
terraform state list         # List resources in state
```

---

## Module 5: Terraform Variables and Expressions

### Variables Overview

```hcl
# Input variables (variables.tf)
variable "instance_type" {
  type        = string
  default     = "t2.micro"
  description = "EC2 instance type"
}

# Output variables (outputs.tf)
output "instance_ip" {
  value = aws_instance.web.public_ip
}

# Local values
locals {
  common_tags = {
    Environment = var.environment
    Project     = var.project_name
  }
}
```

### Advanced Usage

```hcl
# Conditional expression
resource "aws_instance" "web" {
  instance_type = var.environment == "prod" ? "t2.large" : "t2.micro"
}

# Dynamic blocks
dynamic "ingress" {
  for_each = var.ingress_rules
  content {
    from_port   = ingress.value.from_port
    to_port     = ingress.value.to_port
    protocol    = ingress.value.protocol
    cidr_blocks = ingress.value.cidr_blocks
  }
}
```

---

## Module 6: State Management and Backends

### Remote Backend Configuration

```hcl
terraform {
  backend "s3" {
    bucket         = "my-terraform-state"
    key            = "prod/terraform.tfstate"
    region         = "us-west-2"
    encrypt        = true
    dynamodb_table = "terraform-locks"
  }
}
```

---

## Module 7: Terraform Provisioners

```hcl
resource "aws_instance" "web" {
  ami           = "ami-12345678"
  instance_type = "t2.micro"

  # File provisioner
  provisioner "file" {
    source      = "app.conf"
    destination = "/etc/app.conf"
  }

  # Remote exec provisioner
  provisioner "remote-exec" {
    inline = [
      "sudo apt-get update",
      "sudo apt-get install -y nginx"
    ]
  }

  # Local exec provisioner
  provisioner "local-exec" {
    command = "echo ${self.private_ip} >> private_ips.txt"
  }
}
```

---

## Module 8: Modularizing Terraform Configurations

### Module Structure

```
modules/
└── vpc/
    ├── main.tf
    ├── variables.tf
    ├── outputs.tf
    └── README.md
```

### Using Modules

```hcl
module "vpc" {
  source = "./modules/vpc"
  
  vpc_cidr = "10.0.0.0/16"
  environment = "production"
}

# Using registry modules
module "vpc" {
  source  = "terraform-aws-modules/vpc/aws"
  version = "3.0.0"
  
  name = "my-vpc"
  cidr = "10.0.0.0/16"
}
```

---

## Module 9: Workspaces and Environment Management

```bash
# Create workspace
terraform workspace new dev
terraform workspace new prod

# Switch workspace
terraform workspace select dev

# List workspaces
terraform workspace list
```

---

# Terraform Advanced

## Module 10: Multi-Cloud Strategy

### Multi-Cloud Use Cases

- Hybrid cloud deployments with AWS, Azure, and GCP
- High availability across clouds

### Example: Multi-Cloud Configuration

```hcl
# AWS Provider
provider "aws" {
  region = "us-west-2"
}

# GCP Provider
provider "google" {
  project = "my-gcp-project"
  region  = "us-central1"
}

# Resources in both clouds
resource "aws_instance" "web" {
  # AWS instance configuration
}

resource "google_compute_instance" "web" {
  # GCP instance configuration
}
```

---

## Module 11: Automation with Terraform

### CI/CD Integration

- Using Terraform with Jenkins, GitHub Actions, GitLab CI
- Testing with Terratest

### GitHub Actions Example

```yaml
name: Terraform
on:
  push:
    branches: [main]

jobs:
  terraform:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      
      - name: Setup Terraform
        uses: hashicorp/setup-terraform@v1
        
      - name: Terraform Init
        run: terraform init
        
      - name: Terraform Plan
        run: terraform plan
        
      - name: Terraform Apply
        if: github.ref == 'refs/heads/main'
        run: terraform apply -auto-approve
```

---

## Module 12: Secrets Management and HashiCorp Vault

```hcl
provider "vault" {
  address = "https://vault.example.com"
}

data "vault_generic_secret" "db_creds" {
  path = "secret/database"
}

resource "aws_db_instance" "default" {
  username = data.vault_generic_secret.db_creds.data["username"]
  password = data.vault_generic_secret.db_creds.data["password"]
}
```

---

## Module 13: Advanced Topics

### Custom Terraform Providers

- Building and testing providers from scratch

### Performance Optimization

- Resource dependencies and parallel execution
- Tips for large-scale infrastructure management

### Cost Estimation

- Terraform Cloud Cost Estimation features
- Third-party tools for cost tracking

---

## Module 14: Real-World Projects

### Project 1: Deploying a Scalable Web Application

Deploy a load-balanced, auto-scaled web app on AWS

### Project 2: Multi-Cloud Deployment

Provision infrastructure across AWS and GCP

### Project 3: Kubernetes Cluster Management

Deploy and manage EKS or AKS cluster

### Project 4: Enterprise-Grade Monitoring

Integrate with Prometheus and Grafana

---

# AWS Cloud Computing

## Module 1: Introduction to Cloud Computing and AWS

### 1. Understanding Cloud Computing

**Advantages of Cloud Computing:**

- Cost efficiency
- Scalability
- High availability

### 2. Types of Clouds

| Type | Description | Example |
|------|-------------|---------|
| **Public Cloud** | Shared infrastructure | AWS, Azure, GCP |
| **Private Cloud** | Dedicated infrastructure | On-premise |
| **Hybrid Cloud** | Mix of public and private | - |

### 3. Introduction to AWS

- AWS Global Infrastructure
- Regions, Availability Zones, Edge Locations
- Core AWS Services Overview

---

## Module 2: Identity and Access Management (IAM)

### IAM Basics

| Component | Description |
|-----------|-------------|
| **Users** | Individual identities |
| **Groups** | Collection of users |
| **Roles** | Temporary credentials |
| **Policies** | Permission definitions |

### Best Practices

- ✅ Principle of Least Privilege
- ✅ Enforce MFA (Multi-Factor Authentication)
- ✅ Audit IAM Activity (CloudTrail)

---

## Module 3: Compute with EC2

### EC2 Instance Types

| Type | Use Case |
|------|----------|
| **General Purpose** | Balanced compute, memory, networking |
| **Compute Optimized** | High-performance processing |
| **Memory Optimized** | Large datasets in memory |
| **Storage Optimized** | High sequential read/write |

### Launching EC2 Instances

```bash
# Connect via SSH
ssh -i "key.pem" ec2-user@<public-ip>
```

---

## Module 4: Networking with AWS VPC

### VPC Components

| Component | Description |
|-----------|-------------|
| **VPC** | Isolated virtual network |
| **Subnets** | Public or private segments |
| **Route Tables** | Traffic routing rules |
| **Internet Gateway** | Internet connectivity |
| **NAT Gateway** | Outbound internet for private subnets |
| **Security Groups** | Instance-level firewall |
| **Network ACLs** | Subnet-level firewall |

### Advanced Networking

- VPC Peering
- Traffic Mirroring
- VPC Flow Logs
- VPN Connections

---

## Module 5: AWS Storage (S3 and Beyond)

### S3 Features

| Feature | Description |
|---------|-------------|
| **Durability** | 99.999999999% |
| **Scalability** | Unlimited storage |
| **Versioning** | Object version history |
| **Lifecycle Policies** | Automated transitions |
| **Cross-Region Replication** | Geographic redundancy |

### Other Storage Services

| Service | Use Case |
|---------|----------|
| **EBS** | Block storage for EC2 |
| **EFS** | Managed file system |
| **S3 Glacier** | Archive storage |

---

## Module 6: AWS Networking and Route 53

### Route 53 Features

- DNS Basics and Domain Registration
- Health Checks
- Routing Policies (Simple, Weighted, Latency-Based, Failover)
- DNS-Based Load Balancing

---

## Module 7: Monitoring and Logging

### AWS CloudWatch

- Metrics, Logs, and Alarms
- Real-World Scenarios: Monitoring EC2

### AWS CloudTrail

- Logging API Calls
- Security and Compliance

### AWS Config

- Resource Inventory
- Configuration History

---

## Module 8: Serverless Computing with AWS Lambda

### Lambda Benefits

- Auto-Scaling
- Pay-as-You-Go
- Event-Driven

### Use Cases

- Automated File Processing
- Event-Driven Applications
- API Backends with API Gateway

---

## Module 9: Infrastructure as Code with CloudFormation

### CloudFormation Template

```yaml
AWSTemplateFormatVersion: '2010-09-09'
Description: Simple EC2 Instance

Resources:
  MyEC2Instance:
    Type: AWS::EC2::Instance
    Properties:
      InstanceType: t2.micro
      ImageId: ami-12345678
      Tags:
        - Key: Name
          Value: MyInstance
```

---

## Module 10: AWS Developer Tools

| Service | Purpose |
|---------|---------|
| **CodeCommit** | Git repositories |
| **CodeBuild** | Build and test |
| **CodeDeploy** | Deployment automation |
| **CodePipeline** | CI/CD orchestration |

---

## Module 11: Advanced Services

### Containerization

| Service | Description |
|---------|-------------|
| **ECR** | Docker image registry |
| **ECS** | Container orchestration |
| **EKS** | Managed Kubernetes |

### Load Balancing

| Type | Use Case |
|------|----------|
| **Application LB** | HTTP/HTTPS traffic |
| **Network LB** | TCP/UDP traffic |
| **Classic LB** | Legacy applications |

### CloudFront (CDN)

- Content Delivery Networks
- Setting Up Distributions

### AWS Systems Manager

- Centralized Management
- Automating Maintenance Tasks

---

## Module 12: AWS Migration and Databases

### Migration Strategies

- Lift-and-Shift vs. Re-Architecting
- AWS Migration Hub
- Database Migration Service

### Databases

| Service | Type |
|---------|------|
| **RDS** | Relational (MySQL, PostgreSQL, etc.) |
| **DynamoDB** | NoSQL |
| **Aurora** | High-performance relational |
| **ElastiCache** | In-memory caching |

---

# Monitoring Tools

## Module 1: Introduction to Monitoring and Observability

### Why Monitoring is Crucial?

- Importance in DevOps
- Key differences: monitoring, logging, observability

### Monitoring Tools Landscape

| Tool | Description |
|------|-------------|
| **Prometheus** | Time-series metrics collection |
| **Grafana** | Visualization and dashboards |
| **Nagios** | Infrastructure monitoring |
| **Zabbix** | Enterprise monitoring |
| **Datadog** | Cloud-based monitoring |

---

## Module 2: Fundamentals of Prometheus

### What is Prometheus?

- Origin, evolution, and core features
- Role in infrastructure monitoring

### Prometheus Architecture

| Component | Description |
|-----------|-------------|
| **Prometheus Server** | Core scraping and storage |
| **Exporters** | Expose metrics |
| **Alertmanager** | Handle alerts |
| **Pushgateway** | Short-lived jobs |

---

## Module 3: Prometheus Setup and Configuration

### prometheus.yml

```yaml
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'prometheus'
    static_configs:
      - targets: ['localhost:9090']

  - job_name: 'node_exporter'
    static_configs:
      - targets: ['localhost:9100']
```

---

## Module 4: Prometheus Query Language (PromQL)

### Query Types

```promql
# Instant query
http_requests_total{method="GET"}

# Range query
http_requests_total[5m]

# Aggregation
sum(rate(http_requests_total[5m])) by (method)

# Functions
rate(http_requests_total[5m])
irate(http_requests_total[5m])
histogram_quantile(0.95, rate(http_request_duration_bucket[5m]))
```

---

## Module 5: Exporters and Instrumentation

### Default Exporters

| Exporter | Metrics |
|----------|---------|
| **Node Exporter** | System metrics (CPU, memory, disk) |
| **Blackbox Exporter** | Probe endpoints |

### Application Instrumentation

```python
from prometheus_client import Counter, start_http_server

REQUEST_COUNT = Counter('app_requests_total', 'Total app requests')

@app.route('/')
def home():
    REQUEST_COUNT.inc()
    return 'Hello World'

if __name__ == '__main__':
    start_http_server(8000)
    app.run()
```

---

## Module 6: Alerting and Notifications

### Alertmanager Configuration

```yaml
global:
  smtp_smarthost: 'smtp.example.com:587'
  smtp_from: 'alertmanager@example.com'

route:
  receiver: 'team-email'
  group_by: ['alertname']

receivers:
  - name: 'team-email'
    email_configs:
      - to: 'team@example.com'

  - name: 'slack'
    slack_configs:
      - channel: '#alerts'
        api_url: 'https://hooks.slack.com/services/...'
```

### Prometheus Alerting Rules

```yaml
groups:
  - name: example
    rules:
      - alert: HighErrorRate
        expr: rate(http_errors_total[5m]) > 0.1
        for: 5m
        labels:
          severity: critical
        annotations:
          summary: "High error rate detected"
```

---

## Module 7: Introduction to Grafana

### What is Grafana?

- Data visualization platform
- Supports multiple data sources

### Grafana Architecture

| Component | Description |
|-----------|-------------|
| **Data Sources** | Prometheus, InfluxDB, etc. |
| **Dashboards** | Visual representations |
| **Panels** | Individual visualizations |
| **Plugins** | Extended functionality |

---

## Module 8: Grafana Setup and Visualization

### Installing Grafana

```bash
# Docker
docker run -d -p 3000:3000 grafana/grafana

# Access at http://localhost:3000
# Default credentials: admin/admin
```

### Creating Dashboards

1. Add Prometheus as data source
2. Create new dashboard
3. Add panels with PromQL queries
4. Configure visualizations

---

## Module 9: Advanced Grafana Dashboards

### Dashboard JSON Model

```json
{
  "dashboard": {
    "title": "My Dashboard",
    "panels": [
      {
        "type": "graph",
        "title": "Request Rate",
        "targets": [
          {
            "expr": "rate(http_requests_total[5m])"
          }
        ]
      }
    ]
  }
}
```

### Grafana Alerts

- Creating alerts on panels
- Notification channels (Email, Slack, PagerDuty)

---

## Module 10: Scaling and High Availability

### Scaling Prometheus

- Federation
- Sharding techniques
- Best practices for HA setups

### Scaling Grafana

- Multi-user configuration
- Enterprise-scale dashboards

---

## Module 11: Monitoring Kubernetes

### Key Metrics

- Pod health
- Resource usage
- Cluster status

### Tools

| Tool | Purpose |
|------|---------|
| **kube-state-metrics** | Kubernetes object metrics |
| **cAdvisor** | Container metrics |

### Deploying the Stack

```yaml
# Using Helm
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm install prometheus prometheus-community/kube-prometheus-stack
```

---

## Module 12: Securing Monitoring Systems

### Authentication and Authorization

- Grafana user roles and permissions
- Securing Prometheus endpoints

### Best Practices

- ✅ Use TLS/SSL for secure communication
- ✅ Manage sensitive data and credentials
- ✅ Regular security audits
- ✅ Network isolation for monitoring infrastructure

---

## Conclusion

This comprehensive DevOps course covers all essential topics from Linux fundamentals to advanced cloud and monitoring tools. Each module builds upon the previous, providing a solid foundation for becoming a proficient DevOps engineer.

**Key Technologies Covered:**

- 🐧 Linux Administration
- 🔀 Git Version Control
- 🐳 Docker Containerization
- ⚙️ Jenkins CI/CD
- ☸️ Kubernetes Orchestration
- 📜 Ansible Automation
- 🏗️ Terraform IaC
- ☁️ AWS Cloud Computing
- 📊 Prometheus & Grafana Monitoring

> **HAPPY LEARNING!** 🚀
