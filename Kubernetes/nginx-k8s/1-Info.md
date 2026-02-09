# Kubernetes Object Types: What They Are and When to Use Them

---

## 1. `Pod`

```yaml
kind: Pod
```

### What it is

* A **single runnable unit**
* One or more containers
* No self-healing
* No scaling

### Use case

* Learning
* Debugging
* One-off tasks

### Key property

> If it dies, Kubernetes does NOT bring it back.

---

## 2. `Deployment`

```yaml
kind: Deployment
```

### What it is

* A **manager for Pods**
* Ensures desired number of Pods
* Handles updates and restarts

### What it creates internally

```md
Deployment → ReplicaSet → Pods
```

### Use case

* Stateless apps (web servers, APIs)

### Key property

> You scale Deployments, not Pods.

---

## 3. `Service`

```yaml
kind: Service
```

### What it is

* A **stable network endpoint**
* Load-balances traffic to Pods
* Uses **selectors**

### What it does NOT do

* Does not run containers
* Does not create Pods

### Types (important later)

* `ClusterIP` (default)
* `NodePort`
* `LoadBalancer`

---

## 4. `Namespace`

```yaml
kind: Namespace
```

### What it is

* A **logical grouping**
* Isolation boundary

### What it does

* Separates names
* Helps with organization and policies

### What it does NOT do

* No networking isolation by default
* No resource limits by default

---

## 5. `ConfigMap`

```yaml
kind: ConfigMap
```

### What it is

* External configuration storage

### What goes inside

* Env vars
* Config files
* App settings

### Why it exists

> Config should not be baked into images.

---

## 6. `Secret`

```yaml
kind: Secret
```

### What it is

* Same as ConfigMap
* For sensitive data

### Examples

* Passwords
* API keys
* Tokens

### Important

* Base64 encoded, **not encrypted** by default

---

## 7. `Ingress`

```yaml
kind: Ingress
```

### What it is

* HTTP/HTTPS routing layer
* Sits **in front of Services**

### Requires

* An Ingress Controller (NGINX, Traefik, etc.)

### Use case

* Domain-based routing
* TLS termination

---

## 8. `Job`

```yaml
kind: Job
```

### What it is

* Run-to-completion workload

### Behavior

* Runs Pods
* Stops when done
* Retries on failure

### Use case

* Batch jobs
* Migrations
* Scripts

---

## 9. `CronJob`

```yaml
kind: CronJob
```

### What it is

* Scheduled Jobs

### Use case

* Backups
* Periodic cleanup
* Scheduled tasks

---

## Critical insight (MOST IMPORTANT)

Same YAML skeleton:

```yaml
apiVersion
kind
metadata
spec
```

Different `kind` ⇒ **different controller** ⇒ **different behavior**

That’s it.

---

## Mental map (memorize)

```md
Workloads:
  Pod
  Deployment
  Job
  CronJob

Networking:
  Service
  Ingress

Config:
  ConfigMap
  Secret

Organization:
  Namespace
```

---

## One-sentence rule > **`kind` defines WHAT Kubernetes creates and HOW it behaves.**

---
