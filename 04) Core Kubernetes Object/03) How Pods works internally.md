
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

## 2️⃣ `kube-apiserver` Validates and Stores Pod Object

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

## 3️⃣ `kube-scheduler` detects unschedulable pod

Responsibilities:

* watches for Pods without assigned nodes
* finds suitable worker node

`kube-scheduler` evaluates:

* CPU
* memory
* taints/tolerations
* affinity/anti-affinity
* resource availability

And assign a worker node to the pod.

Example:

```text
nginx-pod → worker-node-2
```

Scheduler updates Pod spec:

```yaml
nodeName: worker-node-2
```

---

## 4️⃣ `kubelet` detects assigned pod

Responsibilities:

* watches pods assigned to its node

kubelet notices:

```text
"This Pod should run on my node."
```

---

## 5️⃣ kubelet talks to `container runtime` for pod sandbox creation.

Before containers start, `Kubelet` ask `Container Runtime` to create a Pod Sandbox

The sandbox provides:

| Component         | Purpose                   |
| ----------------- | ------------------------- |
| Network namespace | Shared Pod networking     |
| Pod IP            | Unique Pod address        |
| Hostname          | Pod hostname              |
| Linux namespaces  | Isolation                 |
| Pause container   | Keeps Pod namespace alive |


### 🌐 Pod sandbox creation flow

```text
kube-apiserver
      ↓
kubelet
      ↓
Container Runtime (containerd/CRI-O)
      ↓
Create Pod Sandbox
      ↓
Start Application Containers
```

We will cover this concept in more detail in the next chapter.

---

## 6️⃣ `CNI Plugin` Configures Networking

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
Container runtime creates Pod sandbox
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
