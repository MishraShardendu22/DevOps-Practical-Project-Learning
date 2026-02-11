# Kubernetes Local Tooling – Installation & Usage Guide

This document provides **clean, step‑by‑step, production‑grade installation guides** for the three common Kubernetes tools:

* `kubectl`
* `kubeadm`
* `kind`
* `minikube`

It also explains **when to use what**, with no overlap or confusion.

---

## Prerequisites (Common)

These apply to **all tools**:

```bash
# OS packages
sudo dnf install -y curl wget tar gzip ca-certificates

# Verify architecture
uname -m   # must be x86_64 for commands below
```

---

## Installing kubectl

`kubectl` is the **Kubernetes client CLI**. It is required for **all cluster types**.

### Method 1: Official Binary (Recommended)

```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"

sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
rm kubectl
```

Verify:

```bash
kubectl version --client
```

---

## Installing kind (Kubernetes IN Docker)

`kind` runs Kubernetes **inside Docker containers**. No VM required.

### Requirements

* Docker or containerd installed and running

### Install

```bash
[ "$(uname -m)" = "x86_64" ] && curl -Lo kind https://kind.sigs.k8s.io/dl/v0.31.0/kind-linux-amd64

chmod +x kind
sudo mv kind /usr/local/bin/kind
```

Verify:

```bash
kind version
```

### Create a cluster (optional)

```bash
kind create cluster
```

---

## Installing minikube

`minikube` runs a **single‑node Kubernetes cluster** using a VM or container driver.

### Install (minikube binary) -

```bash
curl -LO https://github.com/kubernetes/minikube/releases/latest/download/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
rm minikube-linux-amd64
```

Verify:

```bash
minikube version
```

### Start cluster (optional)

Using containerd:

```bash
minikube start --driver=containerd
```

---

## Installing kubeadm (Real Kubernetes Cluster)

`kubeadm` is used to bootstrap **real Kubernetes clusters** on VMs or bare metal.

### System Requirements

* Linux host (VM or bare metal)
* 2+ CPU cores
* Swap disabled
* containerd installed

---

### Step 1: Disable swap

```bash
sudo swapoff -a
sudo sed -i '/ swap / s/^/#/' /etc/fstab
```

---

### Step 2: Kernel modules & sysctl

```bash
sudo modprobe overlay
sudo modprobe br_netfilter

cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-iptables=1
net.bridge.bridge-nf-call-ip6tables=1
net.ipv4.ip_forward=1
EOF

sudo sysctl --system
```

---

### Step 3: Install containerd

```bash
sudo dnf install -y containerd
sudo mkdir -p /etc/containerd
containerd config default | sudo tee /etc/containerd/config.toml
```

Enable systemd cgroups:

```bash
sudo sed -i 's/SystemdCgroup = false/SystemdCgroup = true/' /etc/containerd/config.toml
```

Start runtime:

```bash
sudo systemctl enable --now containerd
```

Verify:

```bash
sudo crictl info
```

---

### Step 4: Install kubeadm, kubelet, kubectl

```bash
cat <<EOF | sudo tee /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://pkgs.k8s.io/core:/stable:/v1.29/rpm/
enabled=1
gpgcheck=1
gpgkey=https://pkgs.k8s.io/core:/stable:/v1.29/rpm/repodata/repomd.xml.key
EOF

sudo dnf install -y kubelet kubeadm kubectl
sudo systemctl enable kubelet
```

---

### Step 5: Initialize cluster

```bash
sudo kubeadm init --pod-network-cidr=10.244.0.0/16
```

Wait until:

```md
Your Kubernetes control-plane has initialized successfully!
```

---

### Step 6: Configure kubectl

```bash
mkdir -p $HOME/.kube
sudo cp /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

Verify:

```bash
kubectl get nodes
```

---

### Step 7: Install CNI (Flannel)

```bash
kubectl apply -f https://raw.githubusercontent.com/flannel-io/flannel/master/Documentation/kube-flannel.yml
```

Node should become `Ready`.

---

## Tool Comparison

| Tool     | Nodes | Environment     | Production | Speed  | Use Case       |
| -------- | ----- | --------------- | ---------- | ------ | -------------- |
| kubeadm  | Real  | VM / Bare metal | YES        | Slow   | Prod / Staging |
| minikube | 1     | VM / Container  | NO         | Medium | Learning       |
| kind     | Many  | Docker only     | NO         | Fast   | Testing / CI   |

---

## One‑Line Rule

* **kubeadm** → real clusters
* **minikube** → learn Kubernetes
* **kind** → test Kubernetes

---
