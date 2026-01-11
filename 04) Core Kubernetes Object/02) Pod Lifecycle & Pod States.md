
---

# 📘 Chapter 8: Pod Lifecycle & Pod States (What Happens to a Pod)

---

## 1️⃣ Why Pod Lifecycle Matters

When something goes wrong in Kubernetes:

* Pod not starting
* Pod restarting
* Pod stuck

👉 Understanding Pod lifecycle helps you **quickly identify the problem**.

---

## 2️⃣ Pod Creation Flow (High Level)

When you apply a Pod or Deployment:

1. YAML submitted
2. Scheduler assigns node
3. kubelet starts containers
4. Pod enters running state

This looks simple, but there are **multiple states in between**.

---

## 3️⃣ Pod Phases (Main States)

A Pod has **5 main phases**:

### 1. Pending

* Pod accepted
* Containers not running yet

**Reasons**

* Image pulling
* Waiting for resources
* Scheduling delay

---

### 2. Running

* Pod bound to node
* Containers running

✅ App is working

---

### 3. Succeeded

* All containers completed successfully
* Common for Jobs

---

### 4. Failed

* Container exited with error
* Exit code ≠ 0

---

### 5. Unknown

* Node not reachable
* kubelet stopped responding

---

## 4️⃣ Container States Inside a Pod

Each container has its own state:

* **Waiting**
* **Running**
* **Terminated**

Example:

* Pod = Running
* Container = CrashLoopBackOff

Very common confusion point.

---

## 5️⃣ CrashLoopBackOff (Very Important)

This means:

* Container starts
* Crashes
* Kubernetes restarts it
* Repeats again and again

Common reasons:

* App error
* Wrong command
* Missing config
* Port conflict

---

## 6️⃣ Init Containers (Special Containers)

Init containers:

* Run **before** main containers
* Used for setup tasks

Example:

* Waiting for DB
* Setting permissions
* Downloading files

If init container fails:

* Pod never reaches Running

---

## 7️⃣ Pod Restart Policies

Defined per Pod:

* **Always** (default)
* **OnFailure**
* **Never**

Used mainly with:

* Jobs
* CronJobs

---

## 8️⃣ Pod Deletion (Graceful Shutdown)

When Pod is deleted:

1. SIGTERM sent
2. App gets time to shutdown
3. SIGKILL after timeout

You can control this using:

```yaml
terminationGracePeriodSeconds
```

---

## 9️⃣ How to Debug Pod Lifecycle

Useful commands:

```bash
kubectl get pods
kubectl describe pod <pod-name>
kubectl logs <pod-name>
kubectl logs <pod-name> -p
```

These show:

* Events
* Errors
* Restart reasons

---

## 🔑 Interview One-Liners

* **Pending** → Not running yet
* **Running** → Active
* **CrashLoopBackOff** → Repeated crash
* **Init containers** → Pre-setup

---

## ⚠️ Common Beginner Mistakes

* ❌ Pod Running means app is healthy
* ❌ Ignoring events section
* ❌ Confusing Pod state with container state

---

## ✅ Key Takeaways

* Pod lifecycle has clear phases
* Containers have separate states
* CrashLoopBackOff is very common
* Events are your best debugger

---