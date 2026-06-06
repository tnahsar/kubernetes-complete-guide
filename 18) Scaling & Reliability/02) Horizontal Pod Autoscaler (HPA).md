
---

# 📘 Chapter 20: Horizontal Pod Autoscaler (HPA)

---

## 🔹 1️⃣ Why HPA Exists

Manual scaling is great for predictable workloads, but **real-world traffic is dynamic**:

* Sudden spikes in traffic → manual scaling too slow
* CPU/memory usage varies → optimal Pod count changes
* Goal: **scale automatically based on metrics**

Kubernetes solves this with **Horizontal Pod Autoscaler (HPA)**.

---

## 🔹 2️⃣ What is HPA?

> **Horizontal Pod Autoscaler** automatically adjusts the number of **replicas (Pods)** in a Deployment, ReplicaSet, or StatefulSet based on **observed metrics**.

* Horizontal → scale **number of Pods**
* Vertical → scale **CPU/memory inside a Pod** (Vertical Pod Autoscaler, less common)

---

## 🔹 3️⃣ How HPA Works

1. HPA watches **metrics** like CPU, memory, or custom metrics
2. Compares **current usage** with **target usage**
3. Automatically **increases or decreases replicas** to match target

---

### Example: CPU-based HPA

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: my-app-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: my-app
  minReplicas: 2
  maxReplicas: 10
  metrics:
    - type: Resource
      resource:
        name: cpu
        target:
          type: Utilization
          averageUtilization: 50
```

* `minReplicas` → minimum Pods
* `maxReplicas` → maximum Pods
* `averageUtilization: 50` → target CPU usage per Pod

---

### How it works in practice:

* CPU usage = 70% → HPA adds Pods
* CPU usage = 30% → HPA removes Pods
* Keeps **resource usage optimal** automatically

---

## 🔹 4️⃣ Metrics Server

HPA relies on the **Metrics Server**:

* Collects metrics from nodes and Pods
* Provides **CPU & memory usage** to HPA
* Must be installed in the cluster for HPA to work

Check metrics:

```bash
kubectl top pods
kubectl top nodes
```

---

## 🔹 5️⃣ Scaling Behavior

* HPA **checks metrics every 15 seconds by default**
* Scales **gradually** to avoid flapping (rapid scale up/down)
* Can scale **up and down** within min/max replicas

---

## 🔹 6️⃣ Custom Metrics (Advanced)

Besides CPU/memory, HPA can use:

* Queue length
* Request per second
* External metrics via Prometheus Adapter

Example:

* Autoscale web app based on **number of HTTP requests per second**

---

## 🔹 7️⃣ Benefits of HPA

* Handles **dynamic workloads** automatically
* Reduces **manual intervention**
* Optimizes **resource utilization**
* Improves **reliability and performance**

---

## 🔹 8️⃣ Mental Model (Easy Analogy)

Think of Pods as **taxis in a city**:

* Low passengers → few taxis on the road
* High passengers → automatically dispatch more taxis
* HPA = **smart dispatch manager** reacting to demand

---

## 🔹 9️⃣ Key Takeaways (Interview-Friendly)

* HPA = automatic horizontal scaling
* Uses metrics like CPU, memory, or custom metrics
* Requires **Metrics Server**
* Maintains **desired performance with optimal resources**
* Works on **Deployments, ReplicaSets, StatefulSets**

---