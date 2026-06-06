
---

# 📘 Chapter 6: Worker Node Components (Where Containers Actually Run)

---

## 1️⃣ Why Worker Nodes Matter

All the planning and decisions happen in the control plane,
but **real work happens on worker nodes**.

If control plane says:

> “Run this app”

Worker nodes say:

> “Done ✅”

---

## 2️⃣ What Is a Worker Node?

A **worker node** is a machine that:

* Runs application containers
* Hosts Pods
* Communicates with the control plane

Each worker node is a **fully capable machine** (VM or physical).

---

## 3️⃣ Core Components on Every Worker Node

Each worker node runs **three critical components**:

1. **kubelet**
2. **Container Runtime**
3. **kube-proxy**

Let’s break them down.

---

## 4️⃣ kubelet (Node Agent 🤖)

### What It Is

* An agent running on every worker node
* Talks to the control plane

### What It Does

* Watches for Pods assigned to its node
* Asks container runtime to pull the images
* Starts containers
* Reports status back

### Simple Line

> **kubelet makes sure containers are running as instructed**

---

## 5️⃣ Container Runtime (The Actual Runner 🏃)

### What It Is

* Software that runs containers
* Kubernetes does NOT run containers itself

### Examples

* containerd (most common)
* CRI-O
* Docker (earlier versions)

### Role

* Pull images
* Create containers
* Start/stop containers

Kubernetes → kubelet → container runtime → containers

---

## 6️⃣ kube-proxy (Networking Brain 🧠)

### What It Does

* Handles network rules on the node
* Enables Pod-to-Pod communication
* Enables Service networking

### Example

When traffic comes to:

```text
Service IP → Pod
```

kube-proxy ensures traffic reaches the correct Pod.

---

## 7️⃣ Worker Node Workflow (End-to-End)

When a Pod is scheduled:

1. Scheduler selects node
2. kubelet sees assignment
3. kubelet asks runtime to pull image
4. Container starts
5. kube-proxy manages traffic
6. Status reported back

---

## 8️⃣ Can Worker Nodes Work Alone?

❌ No.

Worker nodes:

* Depend on control plane
* Cannot schedule Pods themselves
* Cannot store cluster state

---

## 9️⃣ Node Failure Scenario (Very Important)

If a worker node dies:

* Control plane detects it
* Controllers create Pods on other nodes
* App stays available

This is **self-healing in action**

---

## 🔑 Interview One-Liners

* **kubelet** → Node manager
* **container runtime** → Runs containers
* **kube-proxy** → Handles networking

---

## ⚠️ Common Beginner Confusion

* ❌ kubelet = scheduler
* ❌ kube-proxy routes external traffic only
* ❌ Docker is mandatory

---

## ✅ Key Takeaways

* Worker nodes run applications
* kubelet is the most important component
* Kubernetes delegates container execution
* Networking is handled at node level

---