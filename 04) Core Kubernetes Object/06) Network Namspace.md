# 📘 What Is a Network Namespace?

A network namespace is a Linux kernel feature that provides:

```text id="q8m’wini"
an isolated network environment
```

for processes or containers.

Each network namespace has its own:

* network interfaces
* IP addresses
* routing table
* firewall rules
* port space
* localhost interface

---

# 🌐 Simple Understanding

Think of a network namespace as:

```text id="m3m’wini"
a separate virtual network stack
```

inside the same Linux machine.

Processes inside one namespace:

* cannot directly see another namespace’s networking

unless explicitly connected.

---

# 🌐 Why Containers Need Network Namespaces

Without network namespaces:

* all containers would share same host networking
* port conflicts would happen
* isolation would break

Example problem:

```text id="v6m’wini"
Container-A → port 80
Container-B → port 80
```

Both cannot bind same port on same network stack.

Network namespaces solve this.

---

# 🌐 Example Without Namespace

```text id="u1m’wini"
Host Network
    ├── Container-A
    └── Container-B
```

Problem:

* shared ports
* shared routing
* poor isolation

---

# 🌐 Example With Network Namespaces

```text id="r4m’wini"
Namespace-1
    └── Container-A (IP: 10.0.0.2)

Namespace-2
    └── Container-B (IP: 10.0.0.3)
```

Now both containers:

* have isolated networking
* can use same ports independently

---

# 📘 Network Namespace in Kubernetes Pod

In Kubernetes:

```text id="k7m’wini"
one Pod gets one shared network namespace
```

All containers inside the Pod share:

* same Pod IP
* same localhost
* same port space

---

# 🌐 Example

Suppose Pod contains:

```text id="n2m’wini"
nginx container
log-agent container
```

Both containers share:

```text id="p5m’wini"
Pod Network Namespace
```

So:

| Feature   | Shared? |
| --------- | ------- |
| Pod IP    | ✅       |
| localhost | ✅       |
| ports     | ✅       |

---

# 🌐 Why Containers Can Use localhost

Because both containers are inside:

```text id="d9m’wini"
same network namespace
```

Example:

```text id="f3m’wini"
nginx → localhost:8080
```

can be accessed by:

* sidecar container
* monitoring container

inside same Pod.

---

# 🌐 Who Creates Network Namespace?

Usually:

```text id="c1m’wini"
container runtime
```

creates it during:

```text id="x6m’wini"
Pod sandbox creation
```

Then:

```text id="j8m’wini"
CNI plugin
```

configures networking and Pod IP.

---

# 🌐 Important Kubernetes Understanding

| Kubernetes Object    | Network Namespace    |
| -------------------- | -------------------- |
| Pod                  | One shared namespace |
| Container inside Pod | Shares Pod namespace |
| Different Pods       | Different namespaces |

---

# 📘 Simplest Mental Model

Think of a network namespace like:

```text id="t4m’wini"
a separate virtual mini-network inside Linux
```

Each Pod gets its own mini-network.

---

# 🔑 One-Line Interview Answer

> A network namespace is a Linux kernel feature that provides an isolated networking environment with its own IP addresses, interfaces, routing table, and port space. In Kubernetes, all containers inside the same Pod share one network namespace and therefore share the same Pod IP and localhost environment.
