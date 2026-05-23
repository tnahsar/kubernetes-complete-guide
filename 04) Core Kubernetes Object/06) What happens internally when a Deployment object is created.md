
---

# 📘 What happens internally when a Deployment object is created

A Deployment is a higher-level Kubernetes object used to manage:

* application releases
* rolling updates
* rollbacks
* scaling
* ReplicaSets
* Pods

Deployment provides declarative application management in Kubernetes.

---

# Example Deployment YAML

```yaml id="ysjlwm"
apiVersion: apps/v1
kind: Deployment

metadata:
  name: backend-deployment

spec:
  replicas: 3

  selector:
    matchLabels:
      app: backend

  template:
    metadata:
      labels:
        app: backend

    spec:
      containers:
        - name: backend-container
          image: nginx:latest

          ports:
            - containerPort: 8080
```

---

# 🌐 Step-by-Step Internal Working of Deployment

---

# 1️⃣ User Creates Deployment Object

Command:

```bash id="4spoz4"
kubectl apply -f deployment.yaml
```

The YAML is sent to:

```text id="t5m’wini"
kube-apiserver
```

---

# 2️⃣ kube-apiserver Validates and Stores Deployment

Component:

```text id="m8m’wini"
kube-apiserver
```

Responsibilities:

* validates Deployment YAML
* creates Deployment object
* stores Deployment state in etcd

---

# 3️⃣ Deployment Controller Detects New Deployment

Component:

```text id="k2m’wini"
Deployment Controller
```

Location:

```text id="c7m’wini"
kube-controller-manager
```

Responsibilities:

* watches Deployment objects
* compares desired state vs actual cluster state

Deployment desired state:

```yaml id="dj13z0"
replicas: 3
```

Current state:

```text id="n4m’wini"
0 Pods running
```

Mismatch detected.

---

# 4️⃣ Deployment Controller Creates ReplicaSet

Deployment itself does NOT directly create Pods.

Instead:

```text id="u1m’wini"
Deployment creates and manages ReplicaSets.
```

Example ReplicaSet created:

```text id="r6m’wini"
backend-deployment-6f77d9f8b
```

Responsibilities of ReplicaSet:

* maintain desired Pod count
* self-healing
* scaling Pods

---

# 🌐 Internal Hierarchy

```text id="x3m’wini"
Deployment
      ↓
ReplicaSet
      ↓
Pods
```

---

# 5️⃣ ReplicaSet Controller Creates Pods

Component:

```text id="y9m’wini"
ReplicaSet Controller
```

Location:

```text id="w4m’wini"
kube-controller-manager
```

Responsibilities:

* watches ReplicaSets
* creates/deletes Pods
* ensures desired replica count

Example:

```yaml id="u2hbxf"
replicas: 3
```

ReplicaSet creates:

```text id="v8m’wini"
backend-pod-1
backend-pod-2
backend-pod-3
```

---

# 6️⃣ Scheduler Assigns Pods to Nodes

Component:

```text id="q5m’wini"
kube-scheduler
```

Responsibilities:

* finds suitable worker nodes
* schedules Pods

Scheduler considers:

* CPU
* memory
* taints/tolerations
* affinity rules
* node resources

Example:

```text id="d1m’wini"
backend-pod-1 → worker-node-1
backend-pod-2 → worker-node-2
```

---

# 7️⃣ kubelet Starts Containers

Component:

```text id="p7m’wini"
kubelet
```

(runs on every worker node)

Responsibilities:

* watches assigned Pods
* talks to container runtime
* starts containers

Example:

```text id="f4m’wini"
containerd
CRI-O
Docker (older setups)
```

---

# 🌐 Container Creation Flow

```text id="s6m’wini"
kubelet
    ↓
container runtime
    ↓
pull image
    ↓
start container
```

---

# 8️⃣ Pod Networking Is Configured

Component:

```text id="j8m’wini"
CNI Plugin
```

Examples:

* Calico
* Flannel
* Cilium

Responsibilities:

* assign Pod IP
* configure networking
* connect Pod to cluster network

Example:

```text id="b3m’wini"
backend-pod-1 → 10.244.0.5
```

---

# 9️⃣ Deployment Status Updated

Deployment Controller continuously monitors:

* ReplicaSets
* Pods
* rollout progress

Deployment status becomes:

```text id="g9m’wini"
AVAILABLE
READY
```

---

# 🌐 Final Running Architecture

```text id="z2m’wini"
Deployment
      ↓
ReplicaSet
      ↓
Pods
      ↓
Containers
```

---

# 📘 What Happens During Scaling?

Example:

```yaml id="k6iq5e"
replicas: 5
```

Deployment Controller updates ReplicaSet.

ReplicaSet Controller creates additional Pods.

---

# 📘 What Happens During Rolling Update?

Suppose image changes:

```yaml id="8ljlwm"
image: nginx:v2
```

Deployment Controller:

* creates NEW ReplicaSet
* gradually creates new Pods
* gradually removes old Pods

Flow:

```text id="m1m’wini"
Old ReplicaSet → scale down
New ReplicaSet → scale up
```

This enables:

* zero/minimal downtime deployments

---

# 📘 What Happens During Rollback?

Command:

```bash id="i2xfpp"
kubectl rollout undo deployment backend-deployment
```

Deployment Controller:

* restores previous ReplicaSet
* scales old ReplicaSet back up
* scales faulty ReplicaSet down

---

# 🌐 Kubernetes Components Involved

| Component             | Responsibility            |
| --------------------- | ------------------------- |
| kube-apiserver        | Stores Deployment object  |
| etcd                  | Stores cluster state      |
| Deployment Controller | Manages Deployments       |
| ReplicaSet Controller | Maintains Pod replicas    |
| kube-scheduler        | Assigns Pods to nodes     |
| kubelet               | Starts containers         |
| container runtime     | Runs containers           |
| CNI plugin            | Configures Pod networking |

---

# 📘 Important Clarification

Deployment itself does NOT:

* run containers
* create containers directly
* schedule Pods

Deployment is a declarative controller object.

Actual work is performed by:

* ReplicaSet Controller
* Scheduler
* kubelet
* container runtime

---

# 🔑 One-Line Interview Answer

> A Kubernetes Deployment manages application releases declaratively by creating ReplicaSets, which create Pods, while Kubernetes controllers, scheduler, kubelet, and container runtime work together to run and maintain the application.
