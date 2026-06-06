
---

# 📘 Chapter 7: What is a Pod? (smallest unit in Kubernetes)

> **A Pod is a wrapper around one container or more containers that share the resources.**

Think of Pod as:

* A box 📦
* Inside the box → one or more containers

---

## 1️⃣ Why Pod exist

In Docker:

* You run **containers**

In Kubernetes:

* You **do not run containers directly**
* Kubernetes runs **Pod**

👉 **Pod is the smallest deployable unit in Kubernetes**, not a container.

---

## 3️⃣ Why does Kubernetes use Pods as the smallest deployable unit instead of managing containers directly?

Kubernetes provides `orchestration features` that become important when running many containers across multiple machines. Kubernetes can't provide these `orchestration features` by directly running the containers, so in order to provide these `orchestration features`, Kubernetes needs the Pod as a wrapper around container.

> `Orchestration features` make running large-scale containerized applications much easier and more reliable.

Container orchestration capabilities provided by Kubernetes:

* Share networking, volume, lifecycle and certain Linux namespaces.
* Self-healing
* Scaling
* Application high availability
* Rolling Updates & Rollbacks
* Version management
* Service Discovery & Networking
* Scheduling Across Nodes

We will learn which Kubernetes objects provide specific orchestration capabilities.

---

## 4️⃣ What Does a Pod Provide?

A Pod gives containers:

### ✅ Shared network

* Same IP address
* Same port space
* `localhost` works between containers

A Pod gets one IP address that represents the entire Pod, not individual containers inside it.

```text
Pod IP: 10.244.1.5
├── container A
└── container B
```

Both containers do not get separate IP addresses. Instead, they share the same network namespace, which includes:
* One IP address
* One port space
* One network identity

Because both the containers share the same network stack. So, inside a Pod they can communicate with each other via `localhost`. No need for internal `service discovery`.

### ✅ Shared volume

A Pod can mount a volume, and that volume can be used by more than one container:

```text
Pod
├── Container A (App)
│     └── /data              ─┐
├── Container B (Log sidecar) ├── shared volume
│     └── /logs              ─┘
```
Both containers see the same files.

* Shared volumes
* Same filesystem mounts

Types of shared volumes:
* Temporary storage (emptyDir): Shared between containers, got deleted when Pod dies.
* Persistent storage (PersistentVolumeClaim): PersistentVolumeClaim (PVC) provides the storage that can survive Pod deletion. This storage can be shared one multiple containers of one pod or optionally it can be shared between Pods as well, depending on the `storage backend system` and `access mode`.

    Case 1: ReadWriteOnce (RWO) — most common
    * Only one Pod can mount it at a time
    * NOT shared across Pods simultaneously
    * Can be shared between containers of the same Pod

    ```text
    Pod A ✔ uses PVC
    Pod B ❌ cannot use same PVC at same time
    ```

    Case 2: ReadWriteMany (RWX)
    * Multiple Pods can mount it simultaneously i.e. shared across Pods
    * Also, can be shared between containers of the same Pod

    ```
    Pod A ─┐
       ├── shared PVC
    Pod B ─┘
    ```

    Case 3: ReadOnlyMany (ROX)
    * Multiple Pods can mount it simultaneously
    * Read-only access across Pods
    * Can be shared between containers of the same Pod

    Storage that supports sharing between Pods (RWX)

    To share a PVC across multiple Pods at the same time, the storage must support ReadWriteMany (RWX).

    Common RWX-capable storage types
    ✔ Network File System (NFS)
    Classic shared filesystem
    Multiple Pods can mount it simultaneously
    ✔ CephFS (Ceph File System)
    Distributed filesystem
    Strong RWX support
    ✔ GlusterFS (less common now)
    Shared filesystem across nodes
    ✔ Cloud file storage services
    Amazon EFS
    Azure Files
    Google Filestore

    These are designed for shared access across multiple Pods/nodes.

    2. Storage that does NOT support sharing between Pods (usually RWO)

    These are block storage systems. They attach a disk to one node/Pod at a time.

    Common RWO storage types
    ✖ Cloud block storage
    Amazon EBS
    Google Persistent Disk
    Azure Disk
    Behavior:
    Only one Pod can mount at a time (in most cases)
    If another Pod tries to mount it → it fails or waits

### ✅ Shared Lifecycle

* Start together
* Stop together
* Restart together

### ✅ Shared Linux Namespaces


---

## 5️⃣ Single-Container Pod (Most Common)

Most Pods have **one container**.

Example:

* One Pod
* One Nginx container

Why?

* Simple
* Scalable
* Easy to manage

---

## 6️⃣ Multi-Container Pod (Sidecar Pattern)

Sometimes Pods have **multiple containers**.

Example:

* App container
* Log collector container

Why?

* Tight coupling
* Need to share storage or network

Containers in a Pod are:

* Deployed together
* Scaled together

---

## 7️⃣ Pod Is Ephemeral (Very Important)

Pods:

* Can die anytime
* Are NOT permanent
* Can be recreated with new IP

👉 **Never store data inside Pods**

Use:

* Volumes
* Persistent storage

---

## 8️⃣ Pod Lifecycle (Simple View)

1. Pod created
2. Container starts
3. App runs
4. Pod terminates
5. New Pod may be created

Kubernetes handles this automatically.

---

## 9️⃣ You Rarely Create Pods Directly

In real life:

* You create **Deployments**
* Deployments create Pods
* Pods come and go

Creating Pods directly:

* Learning
* Debugging
* Special cases

---

## 🔑 Interview One-Liners

* **Pod** → Smallest Kubernetes unit
* **Container runs inside Pod**
* **Pod ≠ container**

---

## ⚠️ Common Beginner Mistakes

* ❌ One Pod = one VM
* ❌ Pods are permanent
* ❌ Access Pods via IP directly

---

## ✅ Key Takeaways

* Kubernetes runs Pods, not containers
* Pod = shared network + storage
* Pods are disposable
* Usually one container per Pod

---