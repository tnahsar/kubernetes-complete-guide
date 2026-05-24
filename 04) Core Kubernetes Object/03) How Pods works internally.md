
---

# 📘 What happens internally when a pod object is created

When a Pod object is created in Kubernetes, multiple Kubernetes components work together to provide:

* Pod scheduling
* Pod networking
* container runtime setup
* shared network namespace
* container creation and startup
* Pod IP allocation
* application execution inside containers

---

## Example Pod YAML

```yaml
apiVersion: v1
kind: Pod

metadata:
  name: nginx-pod

spec:
  containers:
    - name: nginx-container
      image: nginx:latest

      ports:
        - containerPort: 80
```
---

## 🌐 Step-by-Step Internal Working of Pod

## 1️⃣ User Creates Pod Object

Command:

```bash
kubectl apply -f pod.yaml
```

The YAML is sent to:

```text
kube-apiserver
```

---

## 2️⃣ `kube-apiserver` validates and stores pod object

Responsibilities:

* validates Pod YAML
* creates Pod object
* stores Pod state in etcd

At this stage:

```text
Pod exists only as desired state metadata.
```

No container is running yet.

---

## 3️⃣ `kube-scheduler` assigns a node

Responsibilities:

* `kube-scheduler` detects unschedulable pod i.e. watches for pods without assigned nodes
* finds suitable worker node by evaluating:

        * CPU
        * memory
        * taints/tolerations
        * affinity/anti-affinity
        * resource availability

* And assign a worker node to the pod.

Example:

```text
nginx-pod → worker-node-2
```

Scheduler updates `Pod spec`:

```yaml
nodeName: worker-node-2
```

---

## 4️⃣ `kubelet` detects assigned pod

Responsibilities:

* detects a new pod must be created to its node

kubelet notices:

```text
"This Pod should run on my node."
```

---

## 5️⃣ kubelet ask `container runtime` to create `pod sandbox environment`

Before containers start, `Kubelet` ask `Container Runtime` like `containerd or CRI-O` to create a `pod sandbox environment`.

> Consider `pod sandbox environment` as a foundation for the Pod. 

```text
A Pod sandbox is the isolated runtime environment prepared for a pod, where the container runtime creates shared and container-specific Linux namespaces, starts the pause container, and the CNI plugin later configures pod networking before application containers start.

Pod Sandbox Environment Preparation
        │
        ├── Container Runtime
        │      ├── Creates Linux namespaces
        │      └── Starts pause container
        │
        ├── CNI Plugin
        │      ├── Configures networking
        │      ├── Assigns Pod IP
        │      └── Connects Pod to cluster network
        │
        └── Application containers start
```

The Pod sandbox environment includes:

* shared Linux namespaces (network, IPC, UTS, PID optional)
* pause container (stable process attached to shared namespaces)
* Pod networking setup (CNI configuration)
* Pod IP (after CNI configuration)
* hostname configuration (after CNI configuration)

Then application containers run inside this environment.

### 🌐 Think of It Like This

```text
Pod Sandbox (Environment)
        ├── Shared Pod-level namespaces
        │      ├── Network namespace
        │      │      ├── Shared Pod IP
        │      │      ├── Shared localhost
        │      │      └── Shared port space
        │      │
        │      ├── IPC namespace
        │      ├── UTS namespace (hostname)
        │      └── PID namespace (optional)
        │
        ├── Pause container
        │      └── Keeps shared Pod-level namespaces alive
        │
        ├── Container-specific namespaces
        │      └── Mount namespace (typically isolated per container)
        │
        ├── CNI Plugin
        │      ├── Configures pod networking
        │      ├── Assigns pod IP
        │      └── Connects pod to cluster network
        ├── nginx container
        └── sidecar container
```

### `Pause Container` creation as part of pod sandbox environment setup

* During pod sandbox creation, the `container runtime` starts a small special container called the `pause container`, which holds the pod's shared Linux namespaces (network, IPC, UTS, PID optional) and allows all containers inside the pod to share the same networking environment.

> The `pause container` is the first container started in the Pod sandbox and remains running as long as the Pod exists, ensuring that the shared namespaces remain active even if `application containers` crash or restart.

* The `pause container` is crucial for maintaining the `shared network namespace`, which provides the Pod IP and localhost environment that all containers in the Pod share.

---

## 6️⃣ `CNI Plugin` configures networking as part of pod sandbox environment setup

Examples:

* Calico
* Flannel
* Cilium

Responsibilities:

* assigns Pod IP
* creates virtual network interfaces
* configures routing
* enables cross-node Pod communication
* may provide network policies/security

Example:

```text
Pod IP → 10.244.0.5
```

---

## 7️⃣ kubelet talks to `container runtime` for container creation.

Examples:

* containerd
* CRI-O
* Docker (older setups)

Responsibilities:

* pull container image
* create containers
* start containers

### 🌐 Container creation flow

```text
kubelet
    ↓
container runtime
    ↓
pull nginx image
    ↓
create container
    ↓
start container
```

## 7️⃣ Application Opens Ports

Inside the container:

* application process starts
* Linux kernel opens listening sockets

Example:

```text
nginx listens on port 80
```

Important:

```text
Applications open ports — not containers.
```

Ports belong to:

* Pod shared network namespace

### 🌐 Traffic Flow

```text
Client Request
        ↓
Pod IP:80
        ↓
Linux Kernel
        ↓
nginx process inside container
```

---

## 8️⃣ Pod Status Updated

kubelet continuously updates Pod status:

Example:

```text
Pending
Running
Succeeded
Failed
CrashLoopBackOff
```

Status stored through:

```text
kube-apiserver → etcd
```

---

## 🌐 Final Running Architecture

```text
Pod
 ├── Network Namespace
 ├── Storage Volumes
 ├── pause container
 └── Application Containers
```

---

## 📘 Important Clarification

Pod itself does NOT:

* directly run containers
* directly schedule itself

Pod is:

* a declarative Kubernetes object

Actual work is performed by:

* kube-scheduler
* kubelet
* container runtime
* CNI plugins

---

## 🌐 Kubernetes Components Involved

| Component         | Responsibility               |
| ----------------- | ---------------------------- |
| kube-apiserver    | Stores Pod object            |
| etcd              | Stores cluster state         |
| kube-scheduler    | Assigns Pod to node          |
| kubelet           | Manages Pod lifecycle        |
| container runtime | Runs containers              |
| CNI plugin        | Configures networking        |
| Linux kernel      | Manages namespaces & sockets |

---

## 📘 Pod Lifecycle Summary

```
User creates Pod
        ↓
kube-apiserver stores Pod
        ↓
Scheduler assigns node
        ↓
kubelet detects Pod
        ↓
Container runtime creates Pod sandbox environment
        ↓
Container runtime creates `pause container`
        ↓
CNI configures Pod networking
        ↓
Container runtime starts containers
        ↓
Pod becomes Running
```

---

# 🔑 One-Line Interview Answer

> A Kubernetes Pod is the smallest deployable unit that runs one or more containers inside a shared network namespace, while kube-scheduler, kubelet, container runtime, and CNI plugins work together to run and manage the Pod.
