# 📘 What happens internally when a ReplicaSet object is created

A ReplicaSet is a Kubernetes controller object responsible for:

* maintaining desired number of Pods
* self-healing failed Pods
* scaling Pods

ReplicaSet ensures that the specified number of Pod replicas are always running.

---

# Example ReplicaSet YAML

```yaml id="lmjlwm"
apiVersion: apps/v1
kind: ReplicaSet

metadata:
  name: backend-rs

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
```

---

# 🌐 Step-by-Step Internal Working of ReplicaSet

---

# 1️⃣ User Creates ReplicaSet Object

Command:

```bash id="najlwm"
kubectl apply -f replicaset.yaml
```

The YAML is sent to:

```text id="c7m’wini"
kube-apiserver
```

---

# 2️⃣ kube-apiserver Validates and Stores ReplicaSet Object

Component:

```text id="m2m’wini"
kube-apiserver
```

Responsibilities:

* validates ReplicaSet YAML
* creates ReplicaSet object
* stores desired state in etcd

At this stage:

```text id="q5m’wini"
No Pods are running yet.
```

ReplicaSet exists only as cluster desired state.

---

# 3️⃣ ReplicaSet Controller Detects New ReplicaSet

Component:

```text id="v8m’wini"
ReplicaSet Controller
```

Location:

```text id="x1m’wini"
kube-controller-manager
```

Responsibilities:

* watches ReplicaSet objects
* compares desired state vs actual state

ReplicaSet desired state:

```yaml id="e86m8m"
replicas: 3
```

Actual state:

```text id="r4m’wini"
0 Pods running
```

Mismatch detected.

---

# 4️⃣ ReplicaSet Controller Creates Pods

ReplicaSet Controller creates Pods using:

```yaml id="g0r1n4"
template:
```

section from ReplicaSet.

Example Pods created:

```text id="k9m’wini"
backend-pod-1
backend-pod-2
backend-pod-3
```

---

# 🌐 Internal ReplicaSet Flow

```text id="u3m’wini"
ReplicaSet
      ↓
Pod Template
      ↓
Pods Created
```

---

# 5️⃣ Scheduler Assigns Pods to Nodes

Component:

```text id="p6m’wini"
kube-scheduler
```

Responsibilities:

* watches unscheduled Pods
* selects suitable worker nodes

Scheduler considers:

* CPU
* memory
* affinity rules
* taints/tolerations
* resource availability

Example:

```text id="n1m’wini"
backend-pod-1 → worker-node-1
backend-pod-2 → worker-node-2
```

---

# 6️⃣ kubelet Starts Containers

Component:

```text id="t7m’wini"
kubelet
```

(runs on every worker node)

Responsibilities:

* detects assigned Pods
* talks to container runtime
* ensures containers are running

---

# 7️⃣ Container Runtime Runs Containers

Component:

```text id="j4m’wini"
container runtime
```

Examples:

* containerd
* CRI-O
* Docker (older setups)

Responsibilities:

* pull images
* create containers
* start containers

---

# 8️⃣ CNI Plugin Configures Pod Networking

Component:

```text id="w2m’wini"
CNI Plugin
```

Responsibilities:

* assign Pod IP
* configure network interfaces
* connect Pod to cluster network

Example:

```text id="f5m’wini"
backend-pod-1 → 10.244.0.5
```

---

# 🌐 Final Running Architecture

```text id="b9m’wini"
ReplicaSet
      ↓
Pods
      ↓
Containers
```

---

# 📘 Self-Healing Behavior

Suppose one Pod crashes:

```text id="z4m’wini"
backend-pod-2 deleted
```

ReplicaSet Controller notices:

Desired replicas:

```text id="y7m’wini"
3
```

Current running Pods:

```text id="d2m’wini"
2
```

Mismatch detected.

ReplicaSet immediately creates replacement Pod:

```text id="h8m’wini"
backend-pod-4
```

---

# 🌐 Self-Healing Flow

```text id="s1m’wini"
Pod crashes
      ↓
ReplicaSet detects missing Pod
      ↓
New Pod created automatically
```

---

# 📘 Scaling Behavior

Suppose replicas changed:

```yaml id="hlh2wk"
replicas: 5
```

ReplicaSet Controller creates additional Pods.

Suppose replicas reduced:

```yaml id="ic0a7q"
replicas: 2
```

ReplicaSet Controller deletes extra Pods.

---

# 🌐 Scaling Flow

```text id="q9m’wini"
replicas increased
      ↓
ReplicaSet creates Pods

replicas decreased
      ↓
ReplicaSet removes Pods
```

---

# 📘 Label Selector Matching

ReplicaSet uses:

```yaml id="ztltv2"
selector:
  matchLabels:
    app: backend
```

to identify Pods it manages.

Only matching Pods are controlled.

---

# 📘 Important Clarification

ReplicaSet does NOT:

* perform rolling updates
* manage application releases
* manage rollbacks

These are responsibilities of:

```text id="v5m’wini"
Deployment
```

ReplicaSet mainly provides:

* scaling
* self-healing
* desired Pod count management

---

# 🌐 Kubernetes Components Involved

| Component             | Responsibility              |
| --------------------- | --------------------------- |
| kube-apiserver        | Stores ReplicaSet object    |
| etcd                  | Stores cluster state        |
| ReplicaSet Controller | Maintains desired Pod count |
| kube-scheduler        | Assigns Pods to nodes       |
| kubelet               | Manages Pod lifecycle       |
| container runtime     | Runs containers             |
| CNI plugin            | Configures networking       |

---

# 📘 ReplicaSet Lifecycle Summary

```text id="m6m’wini"
User creates ReplicaSet
        ↓
kube-apiserver stores object
        ↓
ReplicaSet Controller detects desired replicas
        ↓
Pods created
        ↓
Scheduler assigns nodes
        ↓
kubelet starts containers
        ↓
ReplicaSet continuously monitors Pods
```

---

# 🔑 One-Line Interview Answer

> A ReplicaSet ensures the desired number of Pod replicas are always running by continuously monitoring Pods and automatically creating or deleting Pods when needed.
