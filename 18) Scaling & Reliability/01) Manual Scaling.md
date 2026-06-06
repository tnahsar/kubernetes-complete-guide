
---

# 📘 Chapter 19: Manual Scaling

---

## 🔹 1️⃣ Why Scaling Matters

Applications experience **variable traffic**:

* Low traffic → use fewer Pods
* High traffic → need more Pods to handle load

Without scaling:

* Apps can **crash under high load**
* Resources are **wasted under low load**

Kubernetes allows **manual and automatic scaling** to manage this efficiently.

---

## 🔹 2️⃣ Manual Scaling Concept

Manual scaling means:

> You decide **how many replicas (Pods)** should run for a Deployment or ReplicaSet.

* Done by the **user / admin**
* Static number of Pods
* Useful for predictable workloads or testing

---

## 🔹 3️⃣ How Manual Scaling Works

### Example: Deployment with 2 replicas

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
spec:
  replicas: 2
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
        - name: app
          image: nginx
```

* Kubernetes ensures **2 Pods are always running**
* If a Pod dies → ReplicaSet creates a replacement

---

### Scaling Up/Down Using `kubectl`

```bash
# Scale up to 5 replicas
kubectl scale deployment my-app --replicas=5

# Scale down to 2 replicas
kubectl scale deployment my-app --replicas=2
```

* Kubernetes will **add or remove Pods** to match the new desired count

---

## 🔹 4️⃣ Checking Pod Count

```bash
kubectl get pods
kubectl get deployment my-app
```

* Deployment shows `DESIRED` vs `CURRENT` vs `AVAILABLE` Pods
* Helps monitor if scaling succeeded

---

## 🔹 5️⃣ Scaling Across ReplicaSets vs Deployments

* **ReplicaSet**: ensures a fixed number of Pods → can scale manually
* **Deployment**: manages ReplicaSets → scaling affects underlying ReplicaSet

> Most users scale **Deployments**, not ReplicaSets directly.

---

## 🔹 6️⃣ Real-World Example

Scenario: You have a web app with **sudden traffic spike**:

* Current replicas: 2
* Requests per second exceed capacity → users see slow response
* Manually scale replicas to 5 → traffic handled
* After traffic normalizes → scale back to 2

✅ Manual scaling gives **immediate control**, but requires **monitoring**.

---

## 🔹 7️⃣ Limitations of Manual Scaling

* Reactive, not proactive → relies on human intervention
* Not efficient for **dynamic workloads**
* Can lead to **over-provisioning or under-provisioning**

> This is why Kubernetes also provides **Horizontal Pod Autoscaler (HPA)** for automatic scaling.

---

## 🔹 8️⃣ Mental Model (Easy Analogy)

Think of Pods as **cashiers in a store**:

* Low customers → 2 cashiers are enough
* Many customers → manually add 3 more cashiers
* Manual scaling = manager decides how many cashiers are working

---

## 🔹 9️⃣ Key Takeaways (Interview-Friendly)

* Manual scaling sets **desired number of Pods**
* Done via `replicas` in Deployment or `kubectl scale`
* Simple, predictable, but not automatic
* ReplicaSets ensure the number of Pods matches the desired state

---