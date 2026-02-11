# Kubernetes Advanced Topics – Monitoring, Security & Service Mesh

This document covers **advanced Kubernetes concepts**: resource management, application health, scheduling, security, observability, and service mesh architecture.

---

## 1. Resource Quotas

### Definition

Restricts total resource consumption **per namespace**. Ensures fair resource distribution across projects and teams.

### Core behavior

* Hard limits on CPU, memory, storage
* Prevents resource starvation
* Pod creation fails if quota exceeded
* Counts pods, services, PVCs, etc.

### Implementation

```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: compute-quota
  namespace: production
spec:
  hard:
    requests.cpu: "100"
    requests.memory: "200Gi"
    limits.cpu: "200"
    limits.memory: "400Gi"
    pods: "100"
    services: "10"
```

### Best practices

* Set quotas per namespace
* Combine with LimitRanges for pod-level defaults
* Monitor quota usage regularly
* Request < Limit for better scheduling

---

## 2. Health Checks (Probes)

### Overview

Three types of probes determine pod health and lifecycle.

---

### 2.1 Liveness Probe

**Purpose**: Detects if pod is **deadlocked/frozen** and needs restart.

**Behavior**:

* Pod running but not responsive → restart
* Useful for long-running processes
* Too aggressive → constant restarts

**Methods**:

```yaml
# HTTP GET
livenessProbe:
  httpGet:
    path: /health
    port: 8080
  initialDelaySeconds: 10
  periodSeconds: 10

# TCP Socket
livenessProbe:
  tcpSocket:
    port: 3306
  periodSeconds: 10

# Command Exec
livenessProbe:
  exec:
    command: ["check-health"]
  periodSeconds: 5
```

**When NOT to use**: Deployments where restart isn't desired on transient issues.

---

### 2.2 Readiness Probe

**Purpose**: Detects if pod is **ready to accept traffic**. Removes from service if not ready.

**Behavior**:

* Pod up but initializing → not included in service
* Allows pod to warm up without receiving requests
* Failed readiness ≠ restart; pod stays running

**Implementation**:

```yaml
readinessProbe:
  httpGet:
    path: /ready
    port: 8080
  initialDelaySeconds: 5
  periodSeconds: 5
  failureThreshold: 3
```

**Use cases**:

* Database migrations on startup
* Cache warming
* Dependency checks

---

### 2.3 Startup Probe (v1.16+)

**Purpose**: Prevents liveness probe from killing slow-starting apps.

**Behavior**:

* Disables liveness/readiness until app starts
* Useful for legacy/heavy apps (Java, .NET)
* Once passed, normal probes take over

**Implementation**:

```yaml
startupProbe:
  httpGet:
    path: /startup
    port: 8080
  failureThreshold: 30
  periodSeconds: 10
  # Allows 30 * 10 = 300 seconds to start
```

---

## 3. Taints and Tolerations

### Taints (Node-level)

**Definition**: Mark a node to **repel pods** unless they tolerate the taint.

**Behavior**:

* Control plane nodes tainted by default (prevent workloads)
* Custom taints for GPU nodes, reserved nodes, etc.
* Pods with matching toleration can schedule

**Syntax**: `key=value:effect`

**Effects**:

* `NoSchedule`: Don't schedule new pods (existing ones run)
* `NoExecute`: Don't schedule; evict existing pods
* `PreferNoSchedule`: Prefer not to schedule (weak)

**Add taint**:

```bash
kubectl taint nodes node1 gpu=true:NoSchedule
kubectl taint nodes node2 dedicated=db:NoExecute
```

### Tolerations (Pod-level)

**Definition**: Allow pods to be scheduled on tainted nodes.

**Implementation**:

```yaml
spec:
  tolerations:
  - key: gpu
    operator: Equal
    value: "true"
    effect: NoSchedule
  - key: dedicated
    operator: Exists
    effect: NoExecute
    tolerationSeconds: 300  # wait 300s before eviction
```

**Operators**:

* `Equal`: exact match
* `Exists`: any value (ignore `value` field)

### Use cases

* GPU nodes only for ML workloads
* Reserved nodes for specific teams
* Graceful drain of problematic nodes

---

## 4. Autoscaling

### Overview

Three scaling mechanisms based on different metrics.

---

### 4.1 HPA (Horizontal Pod Autoscaler)

**Purpose**: Scale **replica count** based on CPU/memory/custom metrics.

**Behavior**:

* Monitors resource usage
* Adds/removes pods (not nodes)
* Requires metrics-server installed
* Scale: 1 → 10 pods based on load

**Installation**:

```bash
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
```

**Example**:

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: app-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: app
  minReplicas: 2
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 70
```

---

### 4.2 VPA (Vertical Pod Autoscaler)

**Purpose**: Automatically adjust **CPU/memory requests** based on usage.

**Behavior**:

* Recommends right-sized requests
* Reduces waste by matching actual usage
* Requires pod restart to apply changes
* Complementary to HPA

**Use cases**:

* Pods with unknown resource needs
* Legacy apps with over-allocated resources

---

### 4.3 KEDA (Kubernetes Event Autoscaling)

**Purpose**: Scale based on **external events** (queue length, HTTP requests, timers).

**Behavior**:

* Integrates with message queues, webhooks, cloud services
* More sophisticated than CPU-based HPA
* Scales to zero efficiently

**Example – AWS SQS**:

```yaml
apiVersion: keda.sh/v1alpha1
kind: ScaledObject
metadata:
  name: sqs-scaler
spec:
  scaleTargetRef:
    name: worker-deployment
  minReplicaCount: 0
  maxReplicaCount: 100
  triggers:
  - type: aws-sqs-queue
    metadata:
      queueURL: https://sqs.us-east-1.amazonaws.com/123456/my-queue
      queueLength: "5"
      awsRegion: "us-east-1"
```

---

## 5. Metrics Server

### Purpose

Collects resource metrics from kubelets for HPA, VPA, and monitoring.

### Installation

```bash
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml

# Verify
kubectl get pods -n kube-system | grep metrics-server
kubectl top nodes   # CPU/memory by node
kubectl top pods    # CPU/memory by pod
```

### Note

Without metrics-server, HPA cannot function.

---

## 6. Node Affinity

### Purpose

Control pod **scheduling on specific nodes** based on labels/properties.

### Types

---

### 6.1 Required Affinity

Pod **must** schedule on matching nodes; fails otherwise.

```yaml
spec:
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: disk-type
            operator: In
            values: ["ssd"]
          - key: zone
            operator: In
            values: ["us-east-1a", "us-east-1b"]
```

---

### 6.2 Preferred Affinity

Pod **prefers** matching nodes; schedules elsewhere if needed.

```yaml
spec:
  affinity:
    nodeAffinity:
      preferredDuringSchedulingIgnoredDuringExecution:
      - weight: 100
        preference:
          matchExpressions:
          - key: gpu
            operator: In
            values: ["true"]
```

**Weights**: 1–100 (higher = preferred).

---

### 6.3 Pod Affinity / Anti-Affinity

Control pod **placement relative to other pods**.

```yaml
# Co-locate with frontend pods
podAffinity:
  requiredDuringSchedulingIgnoredDuringExecution:
  - labelSelector:
      matchExpressions:
      - key: app
        operator: In
        values: ["frontend"]
    topologyKey: kubernetes.io/hostname

# Spread replicas across nodes
podAntiAffinity:
  preferredDuringSchedulingIgnoredDuringExecution:
  - weight: 100
    podAffinityTerm:
      labelSelector:
        matchExpressions:
        - key: app
          operator: In
          values: ["app"]
      topologyKey: kubernetes.io/hostname
```

---

## 7. RBAC (Role-Based Access Control)

### Components

| Component | Scope | Purpose |
|-----------|-------|---------|
| **Role** | Namespace | Define permissions in one namespace |
| **ClusterRole** | Cluster | Define permissions cluster-wide |
| **RoleBinding** | Namespace | Bind role to users/groups in namespace |
| **ClusterRoleBinding** | Cluster | Bind cluster role cluster-wide |
| **ServiceAccount** | Namespace | Pod identity; used with roles |

---

### 7.1 ServiceAccount

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: app-sa
  namespace: production
```

**Pod usage**:

```yaml
spec:
  serviceAccountName: app-sa
```

---

### 7.2 Role (Namespace)

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: pod-reader
  namespace: production
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "list", "watch"]
- apiGroups: [""]
  resources: ["pods/logs"]
  verbs: ["get"]
```

---

### 7.3 RoleBinding

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: read-pods
  namespace: production
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: pod-reader
subjects:
- kind: ServiceAccount
  name: app-sa
  namespace: production
```

---

### 7.4 ClusterRole & ClusterRoleBinding

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: resource-reader
rules:
- apiGroups: [""]
  resources: ["nodes", "persistentvolumes"]
  verbs: ["get", "list"]
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: read-resources
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: resource-reader
subjects:
- kind: ServiceAccount
  name: monitoring-sa
  namespace: monitoring
```

---

## 8. Custom Resource Definitions (CRD)

### Purpose

Extend Kubernetes API with **custom objects** for domain-specific needs.

### Example: Database CRD

```yaml
apiVersion: apiextensions.k8s.io/v1
kind: CustomResourceDefinition
metadata:
  name: databases.example.com
spec:
  group: example.com
  names:
    kind: Database
    plural: databases
  scope: Namespaced
  versions:
  - name: v1
    served: true
    storage: true
    schema:
      openAPIV3Schema:
        type: object
        properties:
          spec:
            type: object
            properties:
              engine:
                type: string
              size:
                type: integer
```

**Usage**:

```yaml
apiVersion: example.com/v1
kind: Database
metadata:
  name: prod-db
spec:
  engine: postgres
  size: 100
```

---

## 9. Kubernetes Dashboard

### Installation

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.7.0/aio/deploy/recommended.yaml

kubectl port-forward -n kubernetes-dashboard svc/kubernetes-dashboard 8443:443
```

Access: `https://localhost:8443`

**Create token**:

```bash
kubectl -n kubernetes-dashboard create token admin-user
```

---

## 10. Debugging & Logs

### Log retrieval

```bash
# Pod logs
kubectl logs pod-name
kubectl logs pod-name -c container-name
kubectl logs pod-name --previous          # Crashed container
kubectl logs pod-name --tail=50 --follow  # Stream last 50 lines

# Logs from all pods in deployment
kubectl logs -l app=myapp
```

### Pod debugging

```bash
# Execute command in pod
kubectl exec -it pod-name -- /bin/bash

# Describe pod (events, status)
kubectl describe pod pod-name

# Debug pod with temporary image
kubectl debug pod-name -it --image=busybox
```

### Node debugging

```bash
# Node logs (systemd)
kubectl get node node-name
ssh node-name
journalctl -u kubelet -f

# Resource usage by node
kubectl top nodes
```

---

## 11. Kubernetes API

### Accessing the API

```bash
# Via kubectl proxy (authenticated)
kubectl proxy --port=8001

# Query API
curl http://localhost:8001/api/v1/namespaces/default/pods
```

### API structure

```
/api/v1                   → Core API (pods, services)
/apis/apps/v1            → Apps API (deployments, statefulsets)
/apis/batch/v1           → Batch API (jobs, cronjobs)
/apis/rbac.authorization.k8s.io/v1 → RBAC
```

---

## 12. Image Scanning

### Purpose

Detect **vulnerabilities in container images** before deployment.

### Tools

* **Trivy**: Fast, comprehensive
* **Clair**: Deep registry scanning
* **Anchore**: Policy-based
* **Snyk**: DevOps integration

### Example – Trivy

```bash
trivy image myregistry/myapp:latest
trivy image --severity HIGH,CRITICAL myregistry/myapp:latest
```

### Admission Controller (block vulnerable images)

```yaml
apiVersion: constraints.gatekeeper.sh/v1beta1
kind: K8sRequiredLabels
metadata:
  name: block-untrusted-images
spec:
  match:
    kinds: [{apiGroups: [""], kinds: ["Pod"]}]
  parameters:
    labels: ["image-scan-passed"]
```

---

## 13. Resource Usage Analysis

### Commands

```bash
# Pod resource usage
kubectl top pods
kubectl top pods --all-namespaces

# Node resource usage
kubectl top nodes

# Detailed metrics
kubectl describe node node-name | grep -A 5 "Allocated resources"

# Historical data (requires metrics-server + persistence)
kubectl get --raw /apis/metrics.k8s.io/v1beta1/nodes
```

---

## 14. Monitoring & Logging

### Popular stacks

| Stack | Purpose |
|-------|---------|
| **Prometheus + Grafana** | Metrics + visualization |
| **ELK (Elasticsearch, Logstash, Kibana)** | Centralized logging |
| **Splunk** | Enterprise logging |
| **Datadog** | Full-stack monitoring (SaaS) |
| **New Relic** | APM + monitoring (SaaS) |

### Prometheus example

```yaml
apiVersion: v1
kind: Service
metadata:
  name: prometheus
spec:
  selector:
    app: prometheus
  ports:
  - port: 9090
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: prometheus
spec:
  selector:
    matchLabels:
      app: prometheus
  template:
    metadata:
      labels:
        app: prometheus
    spec:
      containers:
      - name: prometheus
        image: prom/prometheus
        ports:
        - containerPort: 9090
```

---

## 15. Helm

### Purpose

Package manager for Kubernetes; templatize and manage releases.

### Installation

```bash
curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
helm version
```

### Key concepts

* **Chart**: Package (templates + values)
* **Release**: Deployed instance
* **Values**: Configuration overrides

### Commands

```bash
# Search charts
helm search repo nginx

# Install from repo
helm install my-nginx bitnami/nginx

# Install with custom values
helm install my-app ./mychart -f values.yaml

# Upgrade release
helm upgrade my-app ./mychart

# Rollback
helm rollback my-app 1

# Delete
helm uninstall my-app

# List releases
helm list
```

### Create custom chart

```bash
helm create mychart
helm template mychart    # Preview rendered YAML
helm install mychart ./mychart --dry-run
```

---

## 16. Init Containers & Sidecar Containers

### 16.1 Init Containers

**Purpose**: Run setup tasks **before app container starts**.

**Behavior**:

* Run sequentially, must complete successfully
* Not restarted unless pod restarts
* Perfect for setup, permissions, downloads

**Example**:

```yaml
spec:
  initContainers:
  - name: init-setup
    image: busybox
    command: ['sh', '-c', 'echo "Setting up..." && sleep 5']
  containers:
  - name: app
    image: myapp:latest
```

---

### 16.2 Sidecar Containers

**Purpose**: Run **alongside app** for logging, monitoring, proxying, etc.

**Behavior**:

* Run concurrently with main container
* Restart when pod restarts
* Can access same volumes

**Example – Logging sidecar**:

```yaml
spec:
  containers:
  - name: app
    image: myapp:latest
    volumeMounts:
    - name: shared-logs
      mountPath: /var/log/app
  - name: log-collector
    image: fluent-bit:latest
    volumeMounts:
    - name: shared-logs
      mountPath: /var/log/app
  volumes:
  - name: shared-logs
    emptyDir: {}
```

---

## 17. Service Mesh

### Purpose

Dedicated infrastructure for **service-to-service communication**. Provides:

* Traffic management
* Security (mTLS)
* Observability (traces, metrics)
* Resilience (retries, circuit breakers)

### Popular implementations

* **Istio**: Feature-rich, heavyweight
* **Linkerd**: Lightweight, simpler
* **Consul**: HashiCorp integration

---

## 18. Istio Architecture

### Components

| Component | Role |
|-----------|------|
| **Envoy Proxy (Sidecar)** | Intercepts all traffic in/out of pod |
| **Pilot** | Configures proxies; service discovery |
| **Citadel** | Certificate management (mTLS) |
| **Galley** | Configuration validation |
| **Kiali** | Visualization & monitoring |

### Envoy Sidecar

Automatically injected into pods; handles all networking.

**Injection**:

```bash
kubectl label namespace default istio-injection=enabled
```

**Routing (VirtualService)**:

```yaml
apiVersion: networking.istio.io/v1b3
kind: VirtualService
metadata:
  name: app-routing
spec:
  hosts:
  - app
  http:
  - match:
    - uri:
        prefix: /api
    route:
    - destination:
        host: app-v1
        port:
          number: 8080
      weight: 80
    - destination:
        host: app-v2
        port:
          number: 8080
      weight: 20  # Canary deployment
```

### Security (mTLS)

```yaml
apiVersion: security.istio.io/v1beta1
kind: PeerAuthentication
metadata:
  name: default
spec:
  mtls:
    mode: STRICT  # Enforce mTLS
```

---

## 19. Kiali

### Purpose

**Visualization & monitoring** for Istio service mesh.

### Features

* Service topology graph
* Traffic visualization
* Distributed tracing
* Metrics dashboard

### Installation (with Istio)

```bash
istioctl dashboard kiali
```

---

## 20. Busybox

### Purpose

Minimal Linux image (~1.2MB) for debugging, init tasks, temporary containers.

### Common uses

```bash
# Debug pod
kubectl run -it debug --image=busybox -- sh

# Network troubleshooting
wget http://service:8080/health
nc -zv host 3306
nslookup service.namespace.svc.cluster.local
```

### Tools included

* wget, curl → HTTP
* nc → network
* sh → shell
* Various text tools (grep, sed, awk, etc.)

---

## 21. Best Practices Summary

| Aspect | Recommendation |
|--------|-----------------|
| **Resources** | Always set requests/limits; use quotas |
| **Health** | Combine liviness + readiness probes |
| **Security** | Use RBAC, network policies, pod security |
| **Scaling** | HPA for stateless; KEDA for event-driven |
| **Monitoring** | Prometheus + Grafana minimum |
| **Logging** | Centralize with ELK or similar |
| **Service Mesh** | Consider Istio for complex microservices |
| **Storage** | Use PVCs; backup critical data |

---

## Quick Reference

```bash
# Quotas
kubectl apply -f quota.yaml
kubectl describe resourcequota -n namespace

# Probes
kubectl describe pod pod-name | grep -i probe

# Taints/tolerations
kubectl taint nodes node-name key=value:NoSchedule
kubectl get nodes -o json | jq '.items[].spec.taints'

# HPA
kubectl get hpa
kubectl describe hpa app-hpa

# RBAC
kubectl auth can-i get pods --as=system:serviceaccount:default:app-sa

# Logs
kubectl logs pod-name --all-containers=true --tail=100 -f

# Metrics
kubectl top pods --all-namespaces
```

---

**Last Updated**: February 2026
