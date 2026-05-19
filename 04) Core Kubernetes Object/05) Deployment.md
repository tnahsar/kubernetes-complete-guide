
---

# 📘 Chapter 11: Deployment (Rolling Updates / Rollout & Rollbacks)

---

## 1️⃣ Why Deployments Matter

**ReplicaSets** are great for:

* Self-healing
* Scaling
* High availability by maintaining desired Pod count

But ReplicaSets do NOT solve **application release management** problems such as:

* Rolling updates / Rollout
* Rollbacks
* Version management

**Deployments** solve these problems. ✅

### D/B Rollout and Rollback

This is a very important Kubernetes concept.

The easiest way to remember it is:

```text
Rollout = Move Forward
Rollback = Move Backward
```
---

## 2️⃣ Deployment Definition

> Deployment manages application releases - rolling updates / rollout, rollbacks and version management - through ReplicaSets.

Think of it as:

```text
Deployment = ReplicaSet + release Management + Rollbacks
```

---

## 3️⃣ Relationship Between Deployment and ReplicaSet

Architecture:

```text
Deployment
    ↓
ReplicaSet
    ↓
Pods
```

Responsibilities:

| Component  | Responsibility                                 |
| ---------- | ---------------------------------------------- |
| Deployment | Rolling updates / rollout, rollbacks, version management |
| ReplicaSet | Scaling, self-healing, High availability by maintaining desired Pod count   |
| Pod        | Runs application containers                    |

Important:

* Deployment does NOT directly manage Pods
* Deployment manages ReplicaSets
* ReplicaSet manages Pods

### Remember:

> ReplicaSet Focus on Keep Pods Running
 
> Deployment focus on managing application releases"

---

## 4️⃣ How Deployment Works Internally

When a Deployment is created:

1. Deployment creates a ReplicaSet
2. ReplicaSet creates Pods
3. Deployment continuously monitors desired state
4. During updates, Deployment creates a new ReplicaSet
5. Old ReplicaSet is scaled down gradually

### Example: Releasing a new version of a mobile app

* Old version = v1
* New version = v2

Without Deployment:

* updates happen manually
* downtime may occur
* rollback is risky and slow

With Deployment:

* new version is released gradually
* old version stays available during update
* rollback is easy if problems occur

✅ Deployment automates safe application releases.

---

## 5️⃣ Deployment YAML Example

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  name: frontend-deploy

spec:
  replicas: 3

  selector:
    matchLabels:
      app: frontend

  template:
    metadata:
      labels:
        app: frontend

    spec:
      containers:
      - name: nginx
        image: nginx:1.21

  strategy:
    type: RollingUpdate

    rollingUpdate:
      maxUnavailable: 1
      maxSurge: 1
```

---

### Understanding Important Fields in Deployment YAML file

* **replicas** → Desired number of Pods
* **selector** → Used to identify Pods managed by Deployment
* **template** → Blueprint for creating Pods
* **strategy** → Defines how updates happen
* **maxUnavailable** → Maximum Pods allowed to be unavailable during update
* **maxSurge** → Maximum extra Pods created temporarily during update

---

## 6️⃣ Rolling Updates / Rollout (Zero-Downtime Updates)

Deployment update applications gradually instead of replacing all Pods at once.

This process is called:

```text
Rolling Update
```

Benefits:

* minimizes downtime
* keeps application available
* safer production deployments

Example:

```text
Replicas = 3
maxUnavailable = 1
maxSurge = 1
```

Meaning:

* at most 1 Pod can be unavailable
* at most 1 extra Pod can be created temporarily

Update flow:

```text
Old Pod removed → New Pod created → Repeat gradually
```

✅ Users continue accessing the application during updates.

---

## 7️⃣ Checking Rollout Status

Useful commands:

Check rollout status:

```bash
kubectl rollout status deployment frontend-deploy
```

View rollout history:

```bash
kubectl rollout history deployment frontend-deploy
```
---

## 8️⃣ Rollbacks (Easy Recovery)

> Deployment keeps revision history of ReplicaSets.

If a new application version fails:

```bash
kubectl rollout undo deployment frontend-deploy
```

Kubernetes automatically:

* scales down faulty ReplicaSet
* restores previous working ReplicaSet

✅ Fast and reliable recovery.

---

## 9️⃣ Deployment Strategies

### RollingUpdate (Default)

```yaml
strategy:
  type: RollingUpdate
```
Behavior:

* Gradual updates
* Minimal downtime
* Recommended for production

### Recreate

```yaml
strategy:
  type: Recreate
```

Behavior:

* old Pods terminated first
* new Pods created afterward

⚠️ Causes temporary downtime.

Used only when:

* Application cannot run multiple versions simultaneously

---

## 🔟 Scaling with Deployments

Deployment itself does not directly scale Pods.

Instead:

```text
Deployment updates ReplicaSet
ReplicaSet scales Pods
```

Example:

```bash
kubectl scale deployment frontend-deploy --replicas=5
```

Flow:

```text id="2l7l36"
Deployment → ReplicaSet → Pods
```

ReplicaSet then creates or removes Pods to match desired replicas.

---

## Deployment benefits Over Direct ReplicaSet

| Feature                    | ReplicaSet  | Deployment                |
| ------------------         | ----------  | ------------------------- |
| Self-healing               | ✅          | Indirectly via ReplicaSet |
| Scaling                    | ✅          | Indirectly via ReplicaSet |
| Rolling updates / Rollout  | ❌          | ✅                        |
| Rollbacks                  | ❌          | ✅                        |
| Version management         | ❌          | ✅                        |

---

## ⚠️ Common Beginner Mistakes

### ❌ Updating Pods directly

Always update the Deployment instead of individual Pods.

Otherwise:

* Pods may get recreated
* manual changes may be lost

---

### ❌ Selector and labels do not match

If selectors do not match Pod labels:

* Deployment cannot manage Pods correctly

---

### ❌ Using Recreate strategy unnecessarily

Recreate strategy causes downtime.

Prefer:

* RollingUpdate for production workloads

---

### ❌ Assuming Deployment directly manages Pods

Actually:

```text
Deployment → ReplicaSet → Pods
```

Deployment manages ReplicaSets, not Pods directly.

---

## ✅ Key Takeaways

* Deployment is the standard way to run applications in Kubernetes
* Deployment manages ReplicaSets
* Supports rolling updates / rollout and rollbacks
* Provides safer production deployments
* Works with HPA for auto-scaling
* RollingUpdate is the default deployment strategy
* Usually preferred over creating ReplicaSets directly
