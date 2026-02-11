Below are **clean, structured notes** you can keep and revise.
No fluff. Concept → behavior → when to use → when NOT to use.

---

## 1. Deployment

![Image](https://kubernetes.io/images/docs/kubernetes-cluster-architecture.svg)

![Image](https://kubernetes.io/docs/tutorials/kubernetes-basics/public/images/module_06_rollingupdates2.svg)

### Definition

Controller for **stateless applications**. Manages ReplicaSets → Pods.

### Core behavior

* Desired replicas always maintained
* Rolling updates (zero/minimal downtime)
* Rollback to previous versions
* Pods are **replaceable**

### Characteristics

* No stable identity
* No guaranteed storage
* Any pod can die anytime

### Use cases

* Web servers (nginx)
* APIs
* Frontend apps
* Microservices

### Do NOT use when

* App needs disk persistence
* App needs fixed identity
* App must start in order

---

## 2. ReplicaSet

![Image](https://miro.medium.com/1%2Ata2RyYPCmYXLvDU1Ay_ofA.png)

![Image](https://miro.medium.com/v2/resize%3Afit%3A1400/1%2AJ593hIRTEPNP9wO_QIXQ-g.png)

### Definition

Low-level controller ensuring **N identical pods** are running.

### Core behavior

* Creates/deletes pods to match replica count
* No update or rollback logic

### Characteristics

* No versioning
* No rollout strategy
* Pure pod-count enforcement

### Use cases

* **Never directly**
* Auto-created by Deployments

### Rule

You **almost never write a ReplicaSet YAML manually**.

---

## 3. DaemonSet

![Image](https://www.bluematador.com/hs-fs/hubfs/blog/new/An%20Introduction%20to%20Kubernetes%20DaemonSets/DaemonSets.png?name=DaemonSets.png\&width=770)

![Image](https://miro.medium.com/v2/resize%3Afit%3A1400/0%2AIBMEL6nZPwq-JRGm.png)

### Definition

Ensures **exactly one pod per node** (or per selected nodes).

### Core behavior

* New node joins → pod created
* Node removed → pod removed
* No replica count needed

### Characteristics

* Node-bound
* Pod is tied to node lifecycle
* Usually privileged / host-mounted

### Use cases

* Log agents
* Monitoring agents
* Networking (CNI)
* Storage drivers (CSI)
* Security agents

### Do NOT use when

* App should scale independently of nodes
* App is request-driven (web/API)

---

## 4. StatefulSet

![Image](https://miro.medium.com/v2/resize%3Afit%3A1200/1%2AHlgT4PgRsjrHj30vihI5Fw.png)

![Image](https://www.bluematador.com/hs-fs/hubfs/blog/new/An%20Introduction%20to%20Kubernetes%20StatefulSet/StatefulSets.png?name=StatefulSets.png\&width=770)

### Definition

Controller for **stateful applications** requiring identity + storage.

### Core behavior

* Stable pod names (`app-0`, `app-1`)
* Stable network identity (DNS)
* Persistent volumes per pod
* Ordered start/stop/scale

### Characteristics

* Pod identity matters
* Storage survives pod restart
* Scaling is ordered and slower

### Use cases

* Databases (Postgres, MySQL)
* Message queues (Kafka, RabbitMQ)
* Distributed systems (etcd, Zookeeper)

### Do NOT use when

* App is stateless
* You don’t care which pod is which

---

## One-table summary (memorize)

| Type        | Identity   | Storage  | Scaling    | Typical Use   |
| ----------- | ---------- | -------- | ---------- | ------------- |
| Deployment  | No         | No       | Fast       | Web / API     |
| ReplicaSet  | No         | No       | Fast       | Internal only |
| DaemonSet   | Node-based | Optional | Node-count | Agents        |
| StatefulSet | Yes        | Yes      | Ordered    | Databases     |

---

## Decision rules (important)

* Stateless → **Deployment**
* One per node → **DaemonSet**
* Needs identity or disk → **StatefulSet**
* Never create **ReplicaSet** directly

---

Below is a **clean, real-world Kubernetes example** using:

* **Namespace**
* **PV + PVC**
* **Job (one-time backup)**
* **CronJob (scheduled backup)**
* **Service (expose pod)**
* **Dummy backup script (prints + writes to volume)**

Everything is scoped to **one proper namespace**.

---

## 1️⃣ Namespace

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: backup-ns
```

---

## 2️⃣ PersistentVolume (PV)

Cluster-level object (not namespaced).

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: backup-pv
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  hostPath:
    path: /tmp/backup-data
```

---

## 3️⃣ PersistentVolumeClaim (PVC)

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: backup-pvc
  namespace: backup-ns
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
```

---

## 4️⃣ Job (one-time backup task)

Runs **once**, writes to volume.

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: backup-job
  namespace: backup-ns
spec:
  template:
    spec:
      restartPolicy: Never
      containers:
      - name: backup
        image: busybox
        command:
          - sh
          - -c
          - |
            echo "Running one-time backup"
            date >> /data/backup.log
            echo "Backup complete"
        volumeMounts:
        - name: backup-vol
          mountPath: /data
      volumes:
      - name: backup-vol
        persistentVolumeClaim:
          claimName: backup-pvc
```

---

## 5️⃣ CronJob (scheduled backup)

Runs **every minute** (for demo).

```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: backup-cron
  namespace: backup-ns
spec:
  schedule: "*/1 * * * *"
  jobTemplate:
    spec:
      template:
        spec:
          restartPolicy: OnFailure
          containers:
          - name: backup
            image: busybox
            command:
              - sh
              - -c
              - |
                echo "Running scheduled backup"
                date >> /data/cron-backup.log
            volumeMounts:
            - name: backup-vol
              mountPath: /data
          volumes:
          - name: backup-vol
            persistentVolumeClaim:
              claimName: backup-pvc
```

---

## 6️⃣ Pod exposed via Service

Dummy pod + service to show **pod → outside world**.

### Pod

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: backup-viewer
  namespace: backup-ns
  labels:
    app: viewer
spec:
  containers:
  - name: viewer
    image: busybox
    command: ["sh", "-c", "while true; do nc -l -p 8080 -e cat /data/backup.log; done"]
    volumeMounts:
    - name: backup-vol
      mountPath: /data
  volumes:
  - name: backup-vol
    persistentVolumeClaim:
      claimName: backup-pvc
```

### Service

```yaml
apiVersion: v1
kind: Service
metadata:
  name: backup-service
  namespace: backup-ns
spec:
  selector:
    app: viewer
  ports:
    - port: 8080
      targetPort: 8080
  type: ClusterIP
```

---

## Mental model (lock this in)

* **Job** → run once
* **CronJob** → run on schedule
* **PV** → real storage
* **PVC** → claim storage
* **VolumeMount** → container uses storage
* **Service** → expose pod
