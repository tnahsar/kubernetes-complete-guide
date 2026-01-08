
---

## **Why Kubernetes Deprecated Docker Engine**

---

### **Background of Docker Engine**

**Initially, when Docker first came out, Docker Engine included many components, such as:**

* **Docker Daemon** (`dockerd`)
* **REST API**
* **Docker CLI**
* **Container Runtime** (the core component of Docker Engine before 2017)

In addition to these, Docker Engine provided core functionalities such as:

* **Image Management**
* **Container Execution**
* **Networking**
* **Volume Management**

It also included integrated features like:

* **Docker Swarm** (for orchestration)
* **BuildKit** (for building images)

---

### **History of Containerd**

In **2017**, Docker **donated** **containerd** to the **Cloud Native Computing Foundation (CNCF)**.

Before Docker separated **containerd** in 2017, it was embedded inside Docker Engine as part of the container runtime. After 2017, containerd became independent and was donated to the **CNCF**.

### Here's the timeline and context:

* **Containerd** was originally a core component of Docker's container engine. It was responsible for managing the container lifecycle, such as starting and stopping containers, pulling container images, and managing container execution. It was the part of Docker that Kubernetes would eventually rely on for container runtime functionality.

* In **2017**, Docker decided to **separate** **containerd** from the rest of the Docker Engine to focus more on container orchestration and tooling, while containerd could focus purely on being a container runtime. As part of this shift, Docker **donated** containerd to the **Cloud Native Computing Foundation (CNCF)**, which is the same organization that supports Kubernetes.

* **Containerd** was **designed to be a high-level runtime** that could easily interface with lower-level runtimes like **runc**, which handles the actual execution of containers. By donating containerd to the CNCF, Docker aimed to standardize container runtimes for the broader cloud-native ecosystem and give Kubernetes a more modular and stable runtime to interact with.

This move made it easier for **Kubernetes to adopt containerd** as the default container runtime, which is now the preferred choice for many Kubernetes environments.

---

### **Why Kubernetes Moved to Containerd:**

The **primary reason** Kubernetes moved to **containerd** was to **reduce unnecessary complexity** and create a more **focused and lightweight** system. While Docker was a great tool for general-purpose container management, Kubernetes only needed a subset of Docker's functionality—specifically, the **container runtime**.

* Kubernetes didn’t want the extra overhead of components like the Docker daemon, image-building tools, or networking features. Kubernetes just needed a runtime to **start, stop, and manage containers**, and **containerd** was designed specifically for this. It provides the minimum set of features required to achieve this efficiently.

---

#### **Second Reason: Avoiding the Overhead of Maintaining the DockerShim**

* As Kubernetes was using **Docker Engine**, there needed to be an adapter or bridge between Kubernetes **Kubelet** (the component responsible for managing containers on a node) and **Docker Engine**. This bridge was created by the Kubernetes community and called **dockershim**.
* **Dockershim** served as an adapter that allowed communication between Kubernetes and Docker.
* Kubernetes aimed to move toward **standardization**, and in doing so, it wanted to avoid using non-CRI (Container Runtime Interface)-native runtimes, for which it would need to maintain a bridge like dockershim.
* The Kubernetes community decided that they wanted to use a container runtime that spoke the same language as Kubernetes **without the need for a translator** (like dockershim).
* As a result, Kubernetes focused on **containerd**, and started deprecating the Docker runtime interface (dockershim) in **Kubernetes v1.20** (released in December 2020), and removed it entirely in **Kubernetes v1.24** (released in May 2022).

**Note:** Dockershim was a **Kubernetes component**, not part of **Docker** itself.

---

### **Benefits of Kubernetes Moving Toward Containerd:**

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
   * While Docker's container runtime initially supported CRI, Docker itself was not built specifically for CRI compatibility, which caused some friction when Kubernetes needed to interact with Docker.

---

### **Summary of Key Points:**

* **Docker Engine** provided many components and features that Kubernetes didn’t require (e.g., Docker Daemon, CLI, networking, and image-building tools).
* Kubernetes moved to **containerd** to simplify its architecture, reduce overhead, and use a container runtime designed for Kubernetes’ specific needs.
* The move also helped Kubernetes avoid maintaining a bridge like **dockershim**, which was needed for communication between Kubernetes and Docker Engine.
* Kubernetes benefits from **containerd**’s simplicity, performance, stability, and alignment with Kubernetes' focus on container orchestration.

---