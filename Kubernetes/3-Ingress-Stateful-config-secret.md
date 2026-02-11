# Ingress (Many Apps on Different Ports) in Kubernetes

**Ingress** manages external HTTP/HTTPS access to services inside a cluster.

Instead of exposing multiple services on different NodePorts or LoadBalancers, you use:

* **One Ingress**
* **One LoadBalancer**
* Route traffic by **host or path**

## How It Works

1. Client → LoadBalancer
2. LoadBalancer → Ingress Controller
3. Ingress rules → Route to correct Service
4. Service → Pod(s)

## Example: Multiple Apps on Different Paths

```md
app1 → port 3000
app2 → port 4000
```

Ingress routes based on path:

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: multi-app-ingress
spec:
  rules:
  - host: example.com
    http:
      paths:
      - path: /app1
        pathType: Prefix
        backend:
          service:
            name: app1-service
            port:
              number: 3000
      - path: /app2
        pathType: Prefix
        backend:
          service:
            name: app2-service
            port:
              number: 4000
```

Access:

```md
example.com/app1 → app1
example.com/app2 → app2
```

## Host-Based Routing

```yaml
- host: app1.example.com → app1
- host: app2.example.com → app2
```

## Important

* Ingress works only for **HTTP/HTTPS**
* Needs an **Ingress Controller** (e.g., NGINX)
* Services can run on different internal ports

---

## StatefulSets in Kubernetes

Used for **stateful applications** where identity and storage matter.

Examples:

* Databases
* Kafka
* Elasticsearch

## Why Not Deployment?

Deployment:

* Pods are interchangeable
* Random names
* No stable identity

StatefulSet:

* Stable pod names
* Ordered startup/shutdown
* Persistent storage per pod

---

## Key Features

## 1. Stable Network Identity

Pods get fixed names:

```md
db-0
db-1
db-2
```

DNS:

```md
db-0.my-service
db-1.my-service
```

## 2. Persistent Storage

Each pod gets its own PVC:

```md
db-0 → pvc-db-0
db-1 → pvc-db-1
```

If pod restarts → same storage reattached.

## 3. Ordered Deployment

Created in order:

```md
db-0 → db-1 → db-2
```

Deleted in reverse.

---

## Example StatefulSet

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: db
spec:
  serviceName: "db"
  replicas: 3
  selector:
    matchLabels:
      app: db
  template:
    metadata:
      labels:
        app: db
    spec:
      containers:
      - name: mysql
        image: mysql:8
        volumeMounts:
        - name: data
          mountPath: /var/lib/mysql
  volumeClaimTemplates:
  - metadata:
      name: data
    spec:
      accessModes: ["ReadWriteOnce"]
      resources:
        requests:
          storage: 1Gi
```

---

## Quick Comparison

| Feature       | Deployment         | StatefulSet                  |
| ------------- | ------------------ | ---------------------------- |
| Pod Identity  | Random             | Stable                       |
| Storage       | Shared / Ephemeral | Dedicated PVC per Pod        |
| Startup Order | Parallel           | Ordered                      |
| Use Case      | Stateless apps     | Databases, clustered systems |

---

If app is stateless → use Deployment
If app needs stable identity + storage → use StatefulSet

## ConfigMaps (Kubernetes)

Used to store **non-sensitive configuration data**.

Examples:

* Environment variables
* Config files
* Command-line arguments

## When to Use

* App settings
* Feature flags
* URLs
* Port numbers

## Create Example

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  DB_HOST: mysql-service
  APP_MODE: production
```

---

## Use as Environment Variables

```yaml
env:
- name: DB_HOST
  valueFrom:
    configMapKeyRef:
      name: app-config
      key: DB_HOST
```

---

## Mount as File

```yaml
volumeMounts:
- name: config-volume
  mountPath: /etc/config

volumes:
- name: config-volume
  configMap:
    name: app-config
```

Each key becomes a file inside `/etc/config`.

---

## Secrets (Kubernetes)

Used to store **sensitive data**.

Examples:

* Passwords
* API keys
* TLS certificates
* Tokens

## Important Secrets

* Stored in base64 (not encrypted by default unless etcd encryption enabled)
* Access controlled via RBAC

---

## Create Example Secrets

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: db-secret
type: Opaque
data:
  DB_PASSWORD: cGFzc3dvcmQ=   # base64("password")
```

---

## Use as Environment Variable

```yaml
env:
- name: DB_PASSWORD
  valueFrom:
    secretKeyRef:
      name: db-secret
      key: DB_PASSWORD
```

---

## Mount as File of Secrets

```yaml
volumes:
- name: secret-volume
  secret:
    secretName: db-secret
```

---

## ConfigMap vs Secret

| Feature        | ConfigMap  | Secret                     |
| -------------- | ---------- | -------------------------- |
| Sensitive data | No         | Yes                        |
| Storage format | Plain text | Base64                     |
| Encryption     | No         | Optional (etcd encryption) |
| Common usage   | App config | Credentials, keys          |

---

## Rule of Thumb

* Non-sensitive config → ConfigMap
* Sensitive values → Secret
