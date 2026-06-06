
---

# 📘 CNI (Container Network Interface) networking

### Main Responsibilities of CNI Plugin

| Responsibility                                  | Purpose                                                     |
| ----------------------------------------------- | ----------------------------------------------------------- |
| Create and configure virtual network interfaces | Sets up network interfaces inside the Pod network namespace |
| Assign Pod IP                                   | Gives a unique IP address to the Pod                        |
| Configure routes                                | Enables packet routing between networks                     |
| Connect Pod to cluster network                  | Enables Pod-to-Pod and cluster communication                |
| Setup NAT/firewall rules *(plugin dependent)*   | Controls traffic flow                                       |
| Enable cross-node networking                    | Allows Pods on different nodes to communicate               |

Without a CNI plugin:

```text
Pods cannot communicate properly.
```

### Common Kubernetes CNI plugins

| CNI Plugin | Speciality                      |
| ---------- | ------------------------------- |
| Flannel    | Simple overlay networking       |
| Weave Net  | Multi-host container networking |
| Calico     | Networking + Network Policies   |
| Cilium     | eBPF-based advanced networking  |

### 1️⃣ Flannel

Flannel is:

* simple
* lightweight
* beginner-friendly

Main goal:

```text
Provide basic Pod networking.
```

Features:

* simple overlay networking
* easy setup
* low complexity

Commonly used for:

* learning Kubernetes
* small clusters

### 🌐 Flannel Architecture

```text
Pod → Flannel Network → Other Pods
```

Flannel mainly focuses on:

* connectivity

NOT advanced security features.

---

### 2️⃣ Weave Net

Weave Net is a more Advanced Overlay Networking, that provides:

* simple multi-host networking
* automatic peer discovery
* easy cluster communication

Main goal:

```text
Enable seamless pod communication across nodes.
```

Features:

* overlay networking
* automatic network discovery
* encrypted Pod traffic *(optional)*
* easy installation
* minimal manual configuration

Very useful for:

* beginner-friendly clusters
* multi-node Kubernetes setups
* environments requiring simple networking

### 🌐 How Weave Net Works

Weave Net creates a virtual overlay network between nodes.

```text
Pod → Weave Overlay Network → Other Pods
```

Even if Pods are running on different worker nodes:

* Weave Net enables communication automatically
* without requiring complex network configuration

### 🌐 Important Feature of Weave Net

Weave Net supports:

* automatic peer-to-peer networking
* encrypted traffic between nodes
* Kubernetes Network Policies *(basic support)*

This provides:

* simple cluster networking
* secure node-to-node communication
* easier multi-node setup

---

### 3️⃣ Calico

Calico is:

* very popular in production
* networking + security focused

Features:

* Pod networking
* NetworkPolicy enforcement
* routing optimization
* security controls

Very common in:

* enterprise Kubernetes
* production clusters

### 🌐 Important Feature of Calico

Calico supports:

```text
Kubernetes Network Policies
```

Example:

* allow frontend → backend
* block unknown traffic

This provides:

* microservice security
* traffic isolation

### 4️⃣ Cilium

Cilium is a modern advanced CNI plugin.

Built using:

```text
eBPF
```

inside Linux kernel.

Features:

* high-performance networking
* advanced security
* observability
* Layer 7 traffic visibility
* modern cloud-native networking

---

### 🌐 Why Cilium Is Popular

Cilium provides:

* very fast networking
* deep observability
* efficient packet processing

Very popular in:

* modern cloud-native platforms
* large Kubernetes environments

---

## Simple Comparison

| Feature               | Flannel     | Weave Net | Calico    | Cilium       |
| --------------------- | ---------   | --------- | --------- | ------------ |
| Easy setup            | ✅ Easy    | ✅ Easy    | Medium    | Medium      |
| Basic networking      | ✅         | ✅         | ✅         | ✅        |
| Overlay networking    | ✅         | ✅ Strong  | Optional  | Optional    |
| Network policies      | ❌ Limited | ⚠️ Basic  | ✅         | ✅         |
| Performance           | Basic       | Good      | Good      | Excellent    |
| eBPF support          | ❌         | ❌         | Partial   | ✅ Strong   |
| Production popularity | Medium      | Medium    | Very High | Growing Fast |

---

### 🌐 Important Understanding

Think of CNI plugins as:

```text
the networking engine of Kubernetes.
```

Kubernetes defines:

* what networking should look like

CNI plugins implement:

* how networking actually works

These plugins run:

* on worker nodes 
* as DaemonSets usually

---

# 🔑 One-Line Interview Answer

> Calico, Flannel, and Cilium are Kubernetes CNI plugins that implement Pod networking by assigning Pod IPs, configuring routing, and enabling communication between Pods across the cluster.
