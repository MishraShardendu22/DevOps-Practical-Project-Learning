# Kubernetes Architecture Explained

![Kubernetes Architecture Diagram](https://miro.medium.com/v2/resize:fit:1400/1*0Sudxeu5mQyN3ahi1FV49A.png)

## Table of Contents

- [Architecture Overview](#architecture-overview)
- [Control Plane (Master Node)](#control-plane-master-node)
- [Worker Node Components](#worker-node-components)
- [Kubernetes Resources](#kubernetes-resources)
- [Networking](#networking)
- [Command-Line Tool (kubectl)](#command-line-tool-kubectl)
- [Cluster Setup Methods](#cluster-setup-methods)
- [Getting Started](#getting-started)

---

## Architecture Overview

### Monolith vs Microservices

- **Monolith**: Single, unified application architecture - everything runs as one big application
- **Microservices**: Distributed architecture with multiple independent services - each service handles a specific function

### Cluster Terminology

- **Node**: A single server (physical or virtual machine) in the cluster
- **Cluster**: Multiple nodes (servers) working together as a unified system
- **Master Node (Control Plane)**: The "headquarters" that manages and controls the cluster
- **Worker Node**: The "branch offices" that execute workloads (run containers)

---

## Control Plane (Master Node)

The master node acts as the headquarters that controls and coordinates all worker nodes. It makes global decisions about the cluster and detects/responds to cluster events.

### Key Components Control Plane:

#### 1. API Server

**Think of it as the "front desk" of Kubernetes.**

An **API** is a **medium/interface that enables communication** between two systems or components, using defined rules and formats.

In Kubernetes specifically:

- The **API Server** is the **only communication entry point**.
- All users and components communicate **through the API**, not directly with each other.

**API = communication interface with strict rules.**

- Acts as the communication gateway for the entire cluster
- Entry point for all administrative tasks (kubectl commands, dashboards, etc.)
- Validates and processes all REST commands used to control the cluster
- Only component that directly talks to etcd

**What it does (strict order):**

- Authenticate – who are you
- Authorize – what are you allowed to do
- Admission control – should this request be allowed/modified
- Persist state – read/write to etcd
- Expose state – via REST API + watches

#### 2. etcd

**Raft Algorithm - <https://www.geeksforgeeks.org/system-design/raft-consensus-algorithm/>**
**etcd** is a **distributed, strongly consistent key-value store** used by Kubernetes as its **single source of truth**.

What it does:

- Stores **all cluster state**: nodes, pods, services, configs, secrets, roles, bindings.
- Persists **desired state + current state**.
- Provides **consensus** using the Raft algorithm so multiple control-plane nodes agree on data.

Key properties:

- **Strong consistency** (not eventual).
- **Highly available** when run as a cluster.
- **Watchable**: components subscribe to changes instead of polling.
- **Critical**: if etcd is down or corrupted, the cluster is effectively dead.

In Kubernetes:

- **Only the API Server talks to etcd**.
- Controllers, scheduler, kubelet → **API Server → etcd** (never directly).

etcd is Kubernetes’ **authoritative database**—if it’s wrong or unavailable, Kubernetes cannot function.

**Note - What exactly is a pod, kubelet and a container**:

- K8s schedules Pods; kubelet runs containers inside them.
- A pod is like a minimal linux machine that can run one or more containers.
- It has its own IP address and storage volumes, and it can be scheduled to run on any node in the cluster.
- The kubelet is responsible for managing the lifecycle of pods on a node, ensuring that the containers within the pod are running and healthy.

**What is the difference between containerization and Dockerization? Are they the same, or is Docker just one of the tools used to containerize applications? (Answer is in the question itself)**:

- Containerization = the concept
- Docker (or similar tools) = one way to do it

#### 3. Controller Manager

**Like a bunch of small robots continuously monitoring the cluster.**

##### What is a Controller?

A controller is a loop that constantly:

- Reads desired state from API Server  
- Checks current state  
- Acts if they don't match  

**Example:**

- Desired: 3 Pods  
- Current: 2 Pods  
- Controller action: create 1 Pod  

**Rules:**

- Controller does not run containers  
- Controller does not schedule  
- Controller only enforces state  

##### Control Loop

An infinite loop doing: **observe → compare → fix → repeat**

Kubernetes runs on control loops everywhere.

##### The "kube" Prefix

`kube` = Kubernetes-related component

**Examples:**

- `kube-apiserver` → Kubernetes API server  
- `kubelet` → Kubernetes node agent  
- `kube-controller-manager` → runs controllers  

**Rule:** If it starts with `kube-`, it's a core Kubernetes component

##### Controller Manager Functions

- Monitors the cluster state and ensures desired state matches actual state
- Contains multiple controllers that handle different tasks:
  - **Node Controller**: Monitors node health
  - **Replication Controller**: Maintains correct number of pods
  - **Endpoints Controller**: Manages service endpoints
  - **Service Account Controller**: Creates default accounts for new namespaces
- If something goes wrong (e.g., a pod crashes), controllers work to fix it automatically

#### 4. Scheduler (kube-scheduler)

**Decides where Pods should run.**

What it is:

- A **control-plane component**
- Decides **where a Pod should run**

What it schedules:

- **Pods only**
- Never containers
- Never nodes

When it runs:

- A Pod is **created**
- Pod has **no node assigned**
- Scheduler picks **one node**

How it decides (high level):

1. **Filter** nodes

   - enough CPU / memory
   - node selectors, taints/tolerations
2. **Score** remaining nodes

   - best fit, least waste
3. **Bind** Pod → Node (via API Server)

What it does *not* do:

- Does **not start containers**
- Does **not monitor Pod health**
- Does **not reschedule running Pods**

After scheduling

- kubelet on chosen node takes over

**Scheduler = decision-maker that assigns Pods to nodes.**

#### 5. Cloud Controller Manager *(Optional)*

**Bridges Kubernetes with cloud providers.**

- Allows Kubernetes to interact with cloud provider APIs (AWS, Azure, GCP)
- Handles cloud-specific tasks like:
  - Creating load balancers
  - Managing routes and IP addresses
  - Managing persistent storage volumes
- Only present when running on cloud platforms

---

## Worker Node Components

Worker nodes are the machines that run your containerized applications. Each worker node contains the services necessary to run pods.

### Key Components Worker Nodes:

#### 1. kubelet

**Node-level agent that manages Pods.**

What it is:

- **Node-level agent**
- Runs on **every worker node**

What it does:

- Watches API Server for **Pods assigned to its node**
- **Creates Pods**
- **Starts containers** via container runtime
- **Monitors container health**
- **Reports status back** to API Server

What it manages:

- Pod lifecycle (start, stop, restart)
- Volumes (mount/unmount)
- Liveness & readiness probes

What it does *not* do:

- Does **not schedule Pods**
- Does **not decide desired state**
- Does **not manage other nodes**

Failure behavior:

- Container crashes → kubelet restarts it
- Node dies → kubelet stops reporting → controller reacts

One-line summary:
**kubelet = node worker that executes Pod specs.**

#### 2. kube-proxy

**The "traffic cop" for network communication.**

What it is:

- **Node-level networking component**
- Runs on **every worker node**

What it does:

- Implements **Service networking**
- Routes traffic **to the right Pods**
- Handles **load balancing** at node level

How it works:

- Watches **Services + Endpoints** via API Server
- Programs **iptables / IPVS rules**
- Traffic hitting a Service IP → forwarded to one Pod

What it enables:

- Stable **Service IP**
- Pod-to-Pod communication
- External → Service → Pod routing

What it does *not* do:

- Does **not create networks**
- Does **not assign Pod IPs**
- Does **not act as a real proxy process** (mostly rule-based)

Important:

- kube-proxy ≠ Ingress
- kube-proxy ≠ CNI plugin

One-line summary:
**kube-proxy = traffic router that makes Services work.**

#### 3. Container Runtime

**The software that actually runs containers.**

Popular container runtimes:

- **containerd**: Lightweight, industry-standard runtime
- **Docker**: Popular container platform
- **CRI-O**: Lightweight runtime specifically for Kubernetes

---

## Kubernetes Resources

### Pod

**The smallest deployable unit in Kubernetes.**

- A group of one or more containers that share storage and network
- Think of it like an apartment in an apartment building
- Containers in a pod share:
  - Same IP address and port space
  - Same storage volumes
  - Same network namespace
- Usually runs a single application container (though can run multiple)

### Service

**A stable "phone directory" for pods.**

- Provides a stable network endpoint (IP address and DNS name) for accessing pods
- Since pods can come and go (dynamic), services provide a consistent way to reach them
- Types of services:
  - **ClusterIP**: Internal-only access (default)
  - **NodePort**: Exposes service on each node's IP at a static port
  - **LoadBalancer**: Exposes service externally using cloud provider's load balancer
  - **ExternalName**: Maps service to a DNS name

### Deployment

**Manages the lifecycle of pods.**

- Describes desired state for your application (how many replicas, which image, etc.)
- Creates and manages ReplicaSets, which create and manage pods
- Enables rolling updates and rollbacks
- Ensures specified number of pod replicas are running

### Volume

**Like an external hard drive attached to a pod.**

- Provides persistent storage that outlives individual containers
- Shared among all containers in a pod
- Types include: emptyDir, hostPath, persistentVolumeClaim, cloud storage, and more

### Namespace

**Virtual clusters for resource isolation.**

- Divides cluster resources among multiple users or teams
- Like having different folders on a shared computer - each team sees only their folder
- Provides scope for names (resource names must be unique within a namespace)
- Default namespaces: `default`, `kube-system`, `kube-public`, `kube-node-lease`

### ConfigMap

**Stores non-sensitive configuration data.**

- Key-value pairs for configuration
- Decouples configuration from container images
- Can be consumed as environment variables, command-line arguments, or config files

### Secret

**Stores sensitive information.**

- Similar to ConfigMap but designed for sensitive data (passwords, tokens, SSH keys)
- Base64 encoded (not encrypted by default - enable encryption at rest for production)
- Reduces risk of exposing sensitive data

### Ingress

**The "front door" for external HTTP/HTTPS access.**

- Manages external access to services in the cluster
- Provides load balancing, SSL termination, and name-based virtual hosting
- Routes traffic based on request host or path
- Requires an Ingress Controller to function (nginx, Traefik, HAProxy, etc.)

---

## Networking

### Container Network Interface (CNI)

Enables communication between pods across different nodes in the cluster.

**Popular CNI Plugins:**

- **Weave Net**: Simple overlay network with automatic mesh networking
- **Flannel**: Lightweight network fabric for basic networking
- **Calico**: Provides networking and network policy with advanced security features
- **Cilium**: eBPF-based networking with observability and security

**What CNI Does:**

- Assigns IP addresses to pods
- Ensures pods can communicate across nodes
- Implements network policies for security

---

## Command-Line Tool (kubectl)

**The primary way to interact with Kubernetes clusters.**

- CLI tool for deploying applications, inspecting resources, and managing cluster operations
- Commands follow pattern: `kubectl [command] [resource-type] [resource-name] [flags]`

**Common Commands:**

```bash
# Get resources
kubectl get pods
kubectl get services
kubectl get deployments

# Describe resources (detailed info)
kubectl describe pod <pod-name>

# Create resources
kubectl apply -f <manifest.yaml>

# Delete resources
kubectl delete pod <pod-name>

# View logs
kubectl logs <pod-name>

# Execute commands in container
kubectl exec -it <pod-name> -- /bin/bash
```

---

## Cluster Setup Methods

### Local Development

Perfect for learning and testing:

- **Minikube**: Single-node cluster running in a VM - great for beginners
- **Kind (Kubernetes in Docker)**: Runs multi-node clusters in Docker containers - fast and lightweight
- **k3s/k3d**: Lightweight Kubernetes distribution - production-ready but resource-efficient
- **kubeadm**: Official tool for bootstrapping production-ready clusters - more complex setup

### Cloud Providers

Managed Kubernetes services (easy setup, production-ready):

- **EKS**: Amazon Elastic Kubernetes Service (AWS)
- **AKS**: Azure Kubernetes Service (Microsoft Azure)
- **GKE**: Google Kubernetes Engine (Google Cloud)

### Management Platforms

- **Rancher**: Multi-cluster Kubernetes management platform with UI
- **Civo**: Managed Kubernetes cloud provider with fast provisioning

---

## Getting Started

### Recommended First Step: Setup Kind Cluster

For local development and learning, Kind is excellent:

```bash
# Install kind (if not already installed)
# On Linux:
curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.20.0/kind-linux-amd64
chmod +x ./kind
sudo mv ./kind /usr/local/bin/kind

# Create a cluster
kind create cluster --name my-first-cluster

# Verify cluster is running
kubectl cluster-info
kubectl get nodes

# Deploy a sample application
kubectl create deployment nginx --image=nginx
kubectl expose deployment nginx --port=80 --type=NodePort

# Check the deployment
kubectl get pods
kubectl get services
```

### Learning Path

1. **Understand the architecture** (you're here! ✓)
2. **Install kubectl** and set up a local cluster
3. **Learn to write manifest files** (YAML files that describe resources)
4. **Practice with pods, deployments, and services**
5. **Explore networking** (Services, Ingress)
6. **Learn about storage** (Volumes, PersistentVolumes)
7. **Understand configuration** (ConfigMaps, Secrets)
8. **Explore advanced topics** (StatefulSets, DaemonSets, Jobs, CronJobs)

---

## Quick Reference

| Component | Layer | Purpose |

|-----------|-------|---------|
| API Server | Control Plane | Gateway for all cluster operations |
| etcd | Control Plane | Stores cluster state and configuration |
| Scheduler | Control Plane | Assigns pods to nodes |
| Controller Manager | Control Plane | Maintains desired state |
| Cloud Controller Manager | Control Plane | Cloud provider integration |
| kubelet | Worker Node | Manages pods on the node |
| kube-proxy | Worker Node | Handles networking for pods |
| Container Runtime | Worker Node | Runs containers |

---

**Remember**: Kubernetes is complex, but understanding these fundamentals will help you navigate any Kubernetes cluster with confidence. Start small, practice often, and gradually explore more advanced features!
