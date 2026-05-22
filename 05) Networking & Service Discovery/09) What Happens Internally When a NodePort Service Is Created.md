
---

# 📘 What Happens Internally When a NodePort Service Is Created

When a `NodePort` Service object is created in Kubernetes, multiple Kubernetes components work together to provide:

* virtual ClusterIP
* DNS name
* internal Service routing
* external access using NodeIP + NodePort
* traffic forwarding to Pods

---

## Example NodePort Service Object

```yaml id="jlwm"
apiVersion: v1
kind: Service

metadata:
  name: frontend-service

spec:
  selector:
    app: frontend

  ports:
    - port: 80
      targetPort: 3000
      nodePort: 30080

  type: NodePort
```

---

## 🌐 Step-by-Step Internal Working

---

## 1️⃣ User Creates `NodePort` service object

Command:

```bash id="1w6vzp"
kubectl apply -f service.yaml
```

The YAML is sent to:

```text
kube-apiserver
```

---

## 2️⃣ `kube-apiserver` Stores Service Object

Responsibilities:

* validates Service YAML
* creates Service object
* stores object in `etcd`

Now Kubernetes knows:

```text
A NodePort Service named frontend-service exists.
```

---

# 3️⃣ `Service Controller` Assigns ClusterIP

Even NodePort Services first behave like normal ClusterIP Services internally.

The `Service Controller` inside `kube-controller-manager`:

* assigns virtual ClusterIP
* allocates Service networking configuration

Example:

```text
frontend-service → 10.96.0.20
```

Example Service CIDR range:

```text
10.96.0.0/12
```

---

## 📌 Important Understanding

A `NodePort` Service is actually:

```text
ClusterIP Service + External Node Port
```

So NodePort includes:

* internal cluster networking
* external node-level access

---

## 4️⃣ `CoreDNS` Creates DNS Name

`CoreDNS` watches Kubernetes API resources through the `kube-apiserver`.

When the new Service is detected:

```text
frontend-service
```

CoreDNS automatically creates DNS records.

FQDN:

```text
frontend-service.default.svc.cluster.local
```

Now Pods inside cluster can communicate using:

```text
http://frontend-service
```

instead of Pod IPs.

---

# 5️⃣ `EndpointSlice Controller` Finds Matching Pods

`EndpointSlice Controller` inside `kube-controller-manager`:

Responsibilities:

* watches Service selectors
* finds Pods matching selector labels
* creates EndpointSlice objects

Example selector:

```yaml
selector:
  app: frontend
```

Matching Pods:

```text id="r6m’wini"
frontend-pod-1
frontend-pod-2
frontend-pod-3
```

EndpointSlice stores:

```text id="p8m’wini"
10.244.0.5:3000
10.244.0.6:3000
10.244.0.7:3000
```

---

## 6️⃣ `NodePort` Gets Allocated

If user does NOT specify:
```yaml
nodePort: 30080
```

`kube-apiserver` automatically assign a free port from configured NodePort range.

Example:

```text
30080
```

Default NodePort range:

```text
30000–32767
```

This NodePort becomes externally accessible on cluster nodes.

---

## 7️⃣ `kube-proxy` Creates Networking Rules

`kube-proxy` watches:

* Services
* EndpointSlice objects

Responsibilities:

* creates Service routing rules
* creates NodePort routing rules
* configures packet forwarding

Usually using:

* iptables
* IPVS
* eBPF (modern CNIs)

---

## 🌐 What kube-proxy Configures Internally

kube-proxy creates rules like:

```text
NodeIP:30080
       ↓
frontend-service:80
       ↓
frontend-pod:3000
```

This happens on:

```text
ALL nodes running kube-proxy
```

including:

* worker nodes
* potentially control plane nodes

---

## 🌐 How Traffic Flows Externally

User accesses:

```text
http://NodeIP:30080
```

Traffic flow:

```text
User Browser
      ↓
NodeIP:30080
      ↓
kube-proxy rules
      ↓
frontend-service:80
      ↓
frontend-pod:3000
```

---

## 🌐 Internal Cluster Communication Still Works

Even though NodePort provides external access:

Pods inside cluster still communicate using:

```text
frontend-service
```

or:

```text
ClusterIP:80
```

Internal traffic does NOT use NodePort.

---

## 🌐 Meaning of Ports

Example YAML:

```yaml
ports:
  - port: 80
    targetPort: 3000
    nodePort: 30080
```

| Field            | Meaning                                                 |
| ---------------- | ------------------------------------------------------- |
| port: 80         | Virtual Service port inside cluster                     |
| targetPort: 3000 | Application listening port inside Pod network namespace |
| nodePort: 30080  | External port opened on cluster nodes                   |

---

# 📌 Important Clarification

The Service itself does NOT:

* run containers
* run application code
* directly listen like a normal process

Instead:

```text
NodePort Service is a virtual networking abstraction managed by Kubernetes.
```

Traffic routing is handled by:

* kube-proxy
* iptables/IPVS/eBPF
* CNI networking

---

## 🌐 Who Handles What?

| Kubernetes Component     | Responsibility                           |
| ------------------------ | ---------------------------------------- |
| kube-apiserver           | Stores Service object                    |
| etcd                     | Stores cluster state                     |
| Service Controller       | Assigns ClusterIP                        |
| CoreDNS                  | Creates DNS name                         |
| EndpointSlice Controller | Tracks matching Pods                     |
| kube-proxy               | Creates NodePort + Service routing rules |
| iptables/IPVS/eBPF       | Forwards packets                         |
| CNI Plugin               | Provides Pod networking                  |

---

## ⚠️ Important Real-World Understanding

In production environments:

* usually only frontend/API gateway exposed externally
* backend Services remain ClusterIP
* databases/caches remain internal-only

Because exposing everything using NodePort:

* increases attack surface
* complicates networking
* creates operational overhead

---

## 🌐 Real Production Architecture

```text id="s9m’wini"
Internet
    ↓
NodeIP:30080
    ↓
frontend-service (NodePort)
    ↓
Frontend Pods
    ↓
backend-service (ClusterIP)
    ↓
Backend Pods
```

---

## 🔑 One-Line Interview Answer

> When a NodePort Service is created, Kubernetes assigns a ClusterIP and NodePort, creates DNS entries through CoreDNS, tracks Pods through EndpointSlice objects, and configures kube-proxy routing rules so external traffic reaching NodeIP:NodePort is forwarded to the appropriate Pods.
