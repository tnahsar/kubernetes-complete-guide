
---

# 📘 Chapter 13: Types of Services (ClusterIP, NodePort, LoadBalancer)

---

## 1️⃣ Why This Chapter Matters

Kubernetes Services come in **different types** depending on **how you want to access Pods**:

* Internal only
* External access
* Cloud provider integration

Choosing the **right Service type** is essential for **reliable networking**.

---

## 2️⃣ ClusterIP (Default, Internal Only)

### What It Is

* Default Service type
* Exposes Service **only inside the cluster**
* Pods and other Services can access it

### Use Case

* Backend communication between microservices
* Example: Frontend Pod calls backend Pod

### Example YAML

```yaml
apiVersion: v1
kind: Service
metadata:
  name: backend-service
spec:
  selector:
    app: backend
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8080
  type: ClusterIP
```

* `port` → Port exposed to cluster
* `targetPort` → Port in Pod

---

## 3️⃣ NodePort (External Access via Node)

### What It Is

* Exposes Service on **every node’s IP at a static port**
* Useful for **simple external access**

### How It Works

* NodePort assigned → e.g., 30080
* Access any node IP at `nodeIP:nodePort` → traffic routed to Pods

### Example YAML

```yaml
type: NodePort
ports:
  - port: 80
    targetPort: 8080
    nodePort: 30080
```

### Use Case

* Small deployments without a cloud load balancer
* Testing or dev environments

---

## 4️⃣ LoadBalancer (Cloud Provider Integration)

### What It Is

* Exposes Service externally **via a cloud provider’s load balancer**
* Automatically provisions LB and routes traffic to NodePort + Pods

### Example YAML

```yaml
type: LoadBalancer
ports:
  - port: 80
    targetPort: 8080
```

### Use Case

* Production apps on AWS, GCP, Azure
* Handles traffic scaling and external access

---

## 5️⃣ Summary Table

| Type         | Scope          | Access       | Use Case                  |
| ------------ | -------------- | ------------ | ------------------------- |
| ClusterIP    | Internal       | Cluster only | Microservice internal     |
| NodePort     | Node & Cluster | NodeIP:Port  | Dev / simple external     |
| LoadBalancer | Cloud external | External LB  | Production external       |
| ExternalName | External DNS   | Maps DNS     | Legacy / external systems |

---

## 6️⃣ Key Points

* **ClusterIP** → default, internal
* **NodePort** → exposes a static port on nodes
* **LoadBalancer** → cloud LB, production use
* **ExternalName** → maps to external DNS (less common)

---

## 🔑 One-Line Interview Answer

> **Service types determine how Pods are exposed: ClusterIP (internal), NodePort (external node), LoadBalancer (cloud LB), ExternalName (DNS mapping).**

---

## ⚠️ Common Beginner Mistakes

* ❌ Using NodePort in production without a LB → security & scaling issues
* ❌ Forgetting selector labels → Service won’t route traffic
* ❌ Assuming Pod IPs are stable → Always use Service

---

## ✅ Key Takeaways

* Choose Service type based on **access requirement**
* ClusterIP = internal
* NodePort = dev / external node
* LoadBalancer = production cloud
* ExternalName = map to external DNS

---