
---

## 📘 Chapter 4: Why Kubernetes Deprecated Docker Engine

To understand why Kubernetes deprecated Docker Engine, we need to look at the **evolution of Docker's architecture** and how it aligned with Kubernetes' needs.

---

### 1️⃣ Let's first understand the background of Docker Engine

In 2013, when Docker was first released, **Docker Engine** was composed of multiple tightly coupled components, including:

* **Docker Daemon (`dockerd`)** – the main service that manages containers
* **REST API** – interface exposed by the **Docker daemon**, used by Docker CLI and other tools to communicate with **Docker daemon**
   * Used by:
      * Docker CLI
      * Docker Desktop
      * CI/CD tools
      * SDKs (Go, Python, Java)
      * Remote clients
* **Container Runtime** – Docker’s internal runtime logic (pre-OCI), which later evolved into `runc` (execution) and `containerd` (lifecycle)

Apart from Docker Engine, Docker also provides the **Docker CLI**.
* **Docker CLI was a separate client tool** that communicate with Docker daemon through the REST API.

   ```
   Docker CLI ──▶ REST API ──▶ Docker Daemon
   ```

So when you run:

```bash
docker run nginx
```

The CLI sends a request to **Docker daemon via REST API**, so the request being processed by daemon.

> Important: The REST API isn’t a *separate service* you install—it’s **embedded in the Docker daemon** and enabled by default.

---

### 2️⃣ History of Containerd and runc

### 🕰️ Timeline Explained

### 🔹 2013–2015: Fully Monolithic Docker

* Docker Engine had:

   * Docker Daemon
   * Rest API
   * container runtime

* All tightly coupled inside Docker Engine

**Architectural Flow**

```
Docker CLI
   ↓
Docker daemon (dockerd)
   ↓
Docker container runtime
   ↓
Linux kernel
```

---

### 🔹 2015: `runc` Is Extracted (First Split)

Docker:
* Extracted the **low-level execution logic** into `runc`
* Donated `runc` to OCI (Open Container Initiative)

**But important point:**

* `dockerd` still:

  * Managed container lifecycle
  * Directly invoked `runc` as an external OCI runtime binary.
  * No `containerd` daemon yet

**Architectural Flow**

```
dockerd
   ↓
runc
   ↓
Linux kernel
```

### 🧩 Why Docker Split `runc`?

* To comply with the **OCI Runtime Specification**
* To make `runc` open-source so oKubernetes and other orchestration systems could use it as a standard low-level container runtime
* To decouple low-level container execution from Docker, enabling runtime flexibility through support for different OCI-compatible runtimes such as `runc`, `crun`, `Kata Containers`, and `gVisor`

> After spliting `runc` Docker was **still monolithic in practice**, just cleaner internally.

---

### 🔹 2016–2017: `containerd` is extracted (True Modularization)

* In 2017, Docker donated `containerd` to CNCF (Cloud Native Computing Foundation)
* Docker extracted lifecycle + image management into `containerd`
* `containerd` became a **separate high-level container runtime**


**New architectural flow**
* Here, **containerd invoking runc as an external OCI runtime.**
```
Docker CLI
   ↓
dockerd
   ↓
containerd
   ↓
runc
   ↓
Linux kernel
```

### 🧩 Why Docker Split `containerd`?

* To align with the CNCF and the broader cloud-native ecosystem
* To allow Kubernetes and other orchestration platforms to use a lightweight, vendor-neutral container runtime `containerd` without depending on the full Docker Engine
* To separate container lifecycle management from Docker-specific features and tooling
* By donating `containerd` to CNCF:
  * community trust increased
  * vendor lock-in concerns were reduced
  * industry adoption accelerated
* To Reduce Tight Coupling in Docker Engine
   * Before:
      * Any runtime change impacted the entire engine
      * Hard to maintain and evolve
   * After:
      * Components evolved independently
      * Better maintainability
      * Cleaner architecture

---

### 🔹 After the split, Docker Engine became **layered**.

* Each layer now had **one clear job**.

   | Layer      | Responsibility           |
   | ---------- | ------------------------ |
   | Docker CLI | User interaction         |
   | dockerd    | Docker-specific features |
   | containerd | Container lifecycle      |
   | runc       | Container execution      |

---

### 🧠 Key Insight (This Is the Crux)

> **Splitting `runc` did NOT make Docker non-monolithic.
> Splitting `containerd` did.**

That’s the architectural truth.

### 🎯 Interview-Grade One-Liner

> **Docker extracted `runc` in 2015 but remained architecturally monolithic until `containerd` was separated as a daemon in 2017.**

### ❌ Common Wrong Mental Model

* ❌ “Docker was fully modular after 2015”
* ❌ “containerd existed before runc”

### 🧩 Final Lock-In Table

| Year  | Change             | Architecture     |
| ----- | ------------------ | ---------------- |
| ≤2015 | Internal runtime   | Fully monolithic |
| 2015  | `runc` split       | Semi-modular     |
| 2017  | `containerd` split | Fully modular    |

---

### 🔹 `containerd` — Container Lifecycle Manager

**What it does:**

* Manages container lifecycle:

  * Create
  * Start
  * Stop
  * Delete
* Manages images
* Manages storage & snapshots
* Talks to `runc` when execution is needed

**Key points:**

* Long-running daemon
* Higher-level than `runc`

👉 Think of `containerd` as:

> “The manager that tells `runc` what to run”

---

### 🔹 `runc` — Low-Level Container Execution

**What it does:**

* Creates containers using Linux primitives
  * Namespaces
  * cgroups
  * Mounts
* Starts the container process

**Key points:**

* Very small
* Does one job extremely well
* Implements the **OCI Runtime Specification**

👉 Think of `runc` as:

> “The thing that actually starts a container process”

---

### 📜 OCI Angle (Important)

OCI defines:
* Image spec
* Runtime spec
* How a `runc` runtime **must be invoked**
* What files it consumes (`config.json`, rootfs)

So `containerd` had to:

* Call `runc` externally
* Follow the OCI contract

That's why we can say that, "`runc` is the OCI compliant executor, and `containerd` is the OCI compliant manager that calls runc".

* `Containerd` is an OCI-compliant manager because it natively understands how to build the exact workspace that runc expects, and it knows exactly how to invoke runc according to the official standard.

---

### 🧠 Why Docker donated `runc` and `containerd` to two Different Foundations?

| Component    | Donated To | Why                                  |
| ------------ | ---------- | -------------------------------------|
| `runc`       | **OCI**    | Standardize **execution**            |
| `containerd` | **CNCF**   | Standardize **lifecycle management** |

---

### Why Kubernetes Moved to Containerd:

The **primary reason** Kubernetes moved to **containerd** was to **reduce unnecessary complexity** and create a more **focused** orchestration system. While Docker was a great tool for general-purpose container management, Kubernetes only needed a subset of Docker's functionality i.e. - **container runtime**.

* Kubernetes didn’t want the extra overhead of components like the Docker daemon, docker cli, image-building tools, or networking features. Kubernetes just needed a runtime to **start, stop, and manage containers**, and **containerd** was designed specifically for this. It provides the minimum set of features required to achieve this efficiently.

> **Kubernetes uses containerd as its container runtime, and containerd internally uses runc to execute containers.**

**Architectural flow**
```
Kubernetes
   ↓
containerd
   ↓
runc
   ↓
Linux kernel
```

> Runtime flexibility: Because of `runc` being OCI-complaint: Kubernetes can swap runtimes easily. Like it can select `crun`, `kata-runtime`, `gVisor ` or any other as its low-level runtime.

---

#### **Second Reason: Avoiding the Overhead of Maintaining the DockerShim**

* As Kubernetes was using **Docker Engine**, there needed to be an adapter or bridge between Kubernetes **Kubelet** (the component responsible for managing containers on a node) and **Docker Engine**. This bridge was created by the Kubernetes community and called **dockershim**.
* **Dockershim** served as an adapter that allowed communication between Kubernetes and Docker.
* Kubernetes aimed to move toward **standardization**, and in doing so, it wanted to avoid using non-CRI (Container Runtime Interface)-native runtimes, for which it would need to maintain a bridge like dockershim.
* The Kubernetes community decided that they wanted to use a container runtime that spoke the same language as Kubernetes **without the need for a translator** (like dockershim).
* As a result, Kubernetes focused on **containerd**, and started deprecating the Docker runtime interface (dockershim) in **Kubernetes v1.20** (released in December 2020), and removed it entirely in **Kubernetes v1.24** (released in May 2022).

**Note:** Dockershim was a **Kubernetes component**, not part of **Docker** itself.

---

### Benefits of Kubernetes Moving Toward Containerd:

1. **Simplification of Kubernetes' Architecture**

   * By using containerd, Kubernetes eliminates the unnecessary complexity and overhead associated with Docker’s full suite of features.
   * Kubernetes doesn’t need Docker components like networking, image building, or volume management, so switching to containerd streamlines the system.

2. **Better Performance and Stability**

   * Docker has a large codebase because it includes container management as well as extra tooling for building, distributing, and networking containers.
   * By removing unnecessary components, containerd is **more lightweight** and **faster**, leading to **better scalability** and **reliability** in large-scale Kubernetes clusters.

3. **Containerd is a CNCF Project**

   * **Containerd** is a project under the **Cloud Native Computing Foundation (CNCF)**, the same foundation that supports Kubernetes. It is actively developed and maintained by a large community with a focus on supporting containerized applications in cloud-native environments.
   * **Docker**, although popular, was initially a single company’s project and had a broader scope, including image building, a CLI, and networking. This made Docker more complex for Kubernetes to rely on.

4. **CRI (Container Runtime Interface) Compatibility**

   * Kubernetes uses the **CRI (Container Runtime Interface)** to communicate with different container runtimes.
   * **Containerd** was designed to be a **CRI-compliant runtime**, meaning it works well with Kubernetes out of the box.
   * **Containerd** supports Kubernetes via a **CRI plugin**, allowing kubelet to communicate directly without a shim. Kubernetes talks to containerd via CRI plugin.
   * While Docker's container runtime initially supported CRI, Docker itself was not built specifically for CRI compatibility, which caused some friction when Kubernetes needed to interact with Docker.

---

### **Summary of Key Points:**

* **Docker Engine** provided many components and features that Kubernetes didn’t require (e.g., Docker Daemon, Docker CLI, networking, and image-building tools).
* Kubernetes moved to **containerd** to simplify its architecture, reduce overhead, and use a container runtime designed for Kubernetes’ specific needs.
* The move also helped Kubernetes avoid maintaining a bridge like **dockershim**, which was needed for communication between Kubernetes and Docker Engine.
* Kubernetes benefits from **containerd**’s simplicity, performance, stability, and alignment with Kubernetes' focus on container orchestration.

---

### 🧩 Timeline (One Glance)

| Year | Event                      |
| ---- | -------------------------- |
| 2013 | Docker released            |
| 2015 | OCI formed                 |
| 2017 | containerd donated to CNCF |
| 2020 | containerd graduates       |
| 2021 | dockershim removed         |

---

## 🧩 Final Mental Model

```
Docker = Developer Experience
containerd = Runtime Management
runc = Process Execution
Kubernetes = Orchestration
```

> **Docker builds images, containerd manages containers, runc runs processes, Kubernetes orchestrates everything.**