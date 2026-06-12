
---

# 📘 Chapter 22: Rolling Updates & Rollbacks

---

## 🔹 1️⃣ Why Rolling Updates Are Needed

In production:

* You often need to **update apps** (new version, bug fixes, config changes)
* Goal: **no downtime** for users
* Challenge: Pods are distributed across nodes → must update safely

**Kubernetes solves this with rolling updates.**

---

## 🔹 2️⃣ What Is a Rolling Update?

> **Rolling Update** = gradually replace old Pods with new Pods **without stopping the app**.

* Kubernetes **creates new Pods** with the updated spec
* **Terminates old Pods** gradually
* Ensures **minimum availability** during updates

---

### Example:

* Deployment: 3 replicas running v1
* Update Deployment to v2
* Kubernetes creates 1 new Pod (v2) → deletes 1 old Pod (v1) → repeats
* At any time, **2 Pods remain available** → zero downtime

---

## 🔹 3️⃣ Deployment Spec for Rolling Update

```yaml
spec:
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxUnavailable: 1
      maxSurge: 1
```

* `maxUnavailable` → max Pods unavailable during update
* `maxSurge` → extra Pods allowed temporarily above desired replicas

> This ensures **controlled, gradual updates**.

---

## 🔹 4️⃣ How to Apply Updates

### Using `kubectl`

```bash
# Update container image
kubectl set image deployment/my-app my-app=my-app:v2

# Check rollout status
kubectl rollout status deployment/my-app
```

* Kubernetes performs **rolling update automatically**
* You can monitor progress

---

## 🔹 5️⃣ Rollbacks (Undo Updates)

Sometimes updates **fail or break the app**:

* Kubernetes Deployment **stores history** of previous versions
* You can rollback safely

### Rollback Example

```bash
kubectl rollout undo deployment/my-app
```

* Reverts Deployment to **previous working version**
* Can rollback to a **specific revision** if needed

---

## 🔹 6️⃣ Strategies for Deployment

1. **Rolling Update** (default)

   * Gradually replace Pods
   * Zero downtime

2. **Recreate**

   * Delete all old Pods first → then create new ones
   * Downtime occurs
   * Rarely used in production

> Rolling update is **recommended** for most apps

---

## 🔹 7️⃣ Mental Model (Easy Analogy)

Think of Pods as **cashiers in a bank branch**:

* 3 cashiers (v1) are serving customers

* v2 training starts → bring **1 new cashier** → old cashier goes off duty

* Repeat → all staff upgraded **without closing the branch**

* If a new cashier causes problems → rollback → previous cashier works again

---

## 🔹 8️⃣ Best Practices

✅ Use **RollingUpdate strategy** in production
✅ Set **maxUnavailable / maxSurge** carefully
✅ Monitor Pod health with **readiness & liveness probes**
✅ Keep **revision history** for rollback
✅ Test updates in **staging** before production

---

## 🔹 9️⃣ Key Takeaways (Interview-Friendly)

* Rolling Update → zero-downtime deployment
* Kubernetes gradually replaces old Pods
* Rollback → revert to previous Deployment version
* Deployment strategies: RollingUpdate (default) / Recreate
* Combine with **probes** and **resource limits** for safe updates

---