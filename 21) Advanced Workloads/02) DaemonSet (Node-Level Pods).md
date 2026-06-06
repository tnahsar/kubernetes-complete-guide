Great 👍
Let’s continue with **Chapter 26: DaemonSet (Node-Level Pods)** — this one is **simple but very powerful in real clusters**.

---

# 📘 Chapter 26: DaemonSet (Node-Level Pods)

---

## 🔹 1️⃣ Why DaemonSet Exists

Most workloads in Kubernetes:

* Run **multiple Pods**
* Serve application traffic
* Are managed by Deployments / StatefulSets

But some workloads need to:

* Run on **every node**
* Or **specific nodes**
* Perform **node-level tasks**

Examples:

* Log collection
* Monitoring agents
* Security agents
* Networking components

👉 Deployments **cannot guarantee** one Pod per node
👉 That’s why **DaemonSet exists**

---

## 🔹 2️⃣ What Is a DaemonSet?

> A **DaemonSet** ensures that **exactly one Pod runs on each node** (or selected nodes) in the cluster.

* New node added → Pod automatically scheduled
* Node removed → Pod removed automatically
* No need to manage replicas manually

---

## 🔹 3️⃣ Common Use Cases

Very common DaemonSet workloads:

* **Log collectors** (Fluentd, Fluent Bit)
* **Monitoring agents** (Node Exporter, Datadog Agent)
* **Security agents** (Falco, antivirus)
* **Networking components** (CNI plugins, kube-proxy)

---

## 🔹 4️⃣ How DaemonSet Scheduling Works

DaemonSet:

* Ignores `replicas`
* Schedules **1 Pod per eligible node**
* Uses **node selectors / tolerations** if specified

By default:

* One Pod on every worker node

---

## 🔹 5️⃣ Simple DaemonSet Example

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: log-agent
spec:
  selector:
    matchLabels:
      app: log-agent
  template:
    metadata:
      labels:
        app: log-agent
    spec:
      containers:
      - name: agent
        image: fluent/fluentd
```

👉 This ensures **one Fluentd Pod per node**

---

## 🔹 6️⃣ DaemonSet with Node Selection

Run DaemonSet only on **Linux nodes**:

```yaml
spec:
  template:
    spec:
      nodeSelector:
        kubernetes.io/os: linux
```

Or tolerate master/control-plane nodes:

```yaml
tolerations:
- key: "node-role.kubernetes.io/control-plane"
  operator: "Exists"
  effect: "NoSchedule"
```

---

## 🔹 7️⃣ DaemonSet vs Deployment

| Feature           | Deployment    | DaemonSet         |
| ----------------- | ------------- | ----------------- |
| Replica control   | Yes           | No                |
| One Pod per node  | ❌             | ✅                 |
| Scales with nodes | ❌             | ✅                 |
| Use case          | App workloads | Node-level agents |

---

## 🔹 8️⃣ Updating DaemonSets

DaemonSets support rolling updates:

```yaml
updateStrategy:
  type: RollingUpdate
```

* Updates Pods **node by node**
* Prevents cluster-wide impact

---

## 🔹 9️⃣ Mental Model (Easy Analogy)

Think of nodes as **buildings**:

* DaemonSet = **security guard**
* Every building must have **one guard**
* New building → guard added automatically
* Building removed → guard removed

---

## 🔹 🔟 Best Practices

✅ Use DaemonSet for **infrastructure-level workloads**
✅ Avoid running apps as DaemonSets
✅ Use tolerations carefully
✅ Monitor DaemonSet health

---

## 🔹 1️⃣1️⃣ Key Takeaways (Interview-Friendly)

* DaemonSet = one Pod per node
* Automatically adapts to node changes
* Ideal for logging, monitoring, security
* No replica management
* Supports rolling updates

---

### ✅ Chapter 26 Completed 🎉

---

### 👉 Next Chapter

Next is:

## **Chapter 27: Jobs & CronJobs**

* One-time tasks
* Batch processing
* Scheduled jobs

Just say:
**“Continue with Chapter 27”** 📘🚀
