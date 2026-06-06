
---

# 📘 Chapter 8: Pod Lifecycle & Pod States (What Happens to a Pod)

---

## 🧠 What Is Pod Lifecycle?

Pod lifecycle describes:

> How a Pod moves from creation → running → termination inside Kubernetes.

A Pod does not stay permanent forever.

It gets:

* created
* scheduled
* started
* monitored
* terminated
* replaced (if managed by Deployment/ReplicaSet)

Kubernetes handles this lifecycle automatically.

---

## 🌐 High-Level Pod Lifecycle Flow

```text
Pod Created
      ↓
Scheduled to Node
      ↓
Containers Start
      ↓
Application Runs
      ↓
Pod Terminates
      ↓
New Pod May Be Created
```

---

## 🔥 Why Understanding Pod Lifecycle Matters

In real Kubernetes environments, common issues include:

* Pod not starting
* Pod stuck in Pending
* Containers restarting repeatedly
* Application crashes
* Pod unexpectedly terminating

Understanding Pod lifecycle helps you:

* troubleshoot faster
* identify root causes quickly
* understand Kubernetes behavior better

---

## 📊 Main Pod Phases in Kubernetes

A Pod mainly moves through these 5 phases.

### 1️⃣ Pending

Pod has been accepted by Kubernetes, but containers are not fully running yet.

### Common Reasons

* Image is being pulled
* Waiting for node resources
* Scheduling delay
* Persistent Volume not available
* Init container still running

### Example

```text
STATUS: Pending
```

---

### 2️⃣ Running

Pod has been scheduled successfully and at least one container is running.

### Important Understanding

`Running` does NOT always mean:

* application is healthy
* application is fully ready

It only means:

* containers started successfully

Application issues may still exist internally.

---

### 3️⃣ Succeeded

All containers completed successfully.

Usually seen with:

* Jobs
* Batch processing
* One-time tasks

### Example

```text
STATUS: Completed
```

---

### 4️⃣ Failed

One or more containers terminated with failure.

### Common Reasons

* Application crash
* Invalid startup command
* Missing configuration
* Dependency failure

Example:

```text
Exit Code ≠ 0
```

---

### 5️⃣ Unknown

Kubernetes cannot determine Pod state.

### Common Reasons

* Worker node unreachable
* kubelet stopped responding
* Network issue

This is less common but important in production troubleshooting.

---

## 🧠 Important: Pod Phase vs Container State

This is one of the most common beginner confusion points.

A Pod has:

* Pod phase
* Individual container states

Each container inside the Pod has its own state.

Container states:

* Waiting
* Running
* Terminated

To know the state of any container should run command to describe pod i.e.
```bash
kubectl describe pod <pod-name>
```

Example: To know the states of containers inside `backend-pod`
```bash
kubectl describe pod backend-pod
```

```bash

Output

Containers:
  backend-container:
    Container ID:   containerd://abc123
    Image:          backend:v1
    State:          Running
      Started:      Sat, 06 Jun 2026 10:20:15 +0000
    Ready:          True
    Restart Count:  0
```

---

## 🔥 CrashLoopBackOff (Very Important)

`CrashLoopBackOff` means:

```text
Container Starts
      ↓
Application Crashes
      ↓
Kubernetes Restarts Container
      ↓
Crashes Again
      ↓
Repeated Restart Loop
```

Kubernetes gradually increases restart delay (`BackOff`) between attempts.

---

## 🚀 Init Containers (Special Containers)

Init containers run BEFORE main application containers.

Used for:

* setup tasks
* initialization logic
* dependency preparation

### Common Use Cases

* Waiting for database readiness
* Setting file permissions
* Downloading startup files
* Performing migrations

Flow:

```text
Init Container
      ↓
Main Container Starts
```

### Important Understanding

If init container fails:

* main container never starts
* Pod never reaches Running state

---

## 🔄 Pod Restart Policies

Restart policy controls how Kubernetes handles container failures.

Defined at Pod level.

Available policies:

| Restart Policy | Behavior                 |
| -------------- | ------------------------ |
| Always         | Always restart container |
| OnFailure      | Restart only on failure  |
| Never          | Never restart            |

### Common Usage

| Workload   | Typical Restart Policy |
| ---------- | ---------------------- |
| Deployment | Always                 |
| Job        | OnFailure              |
| CronJob    | OnFailure              |

---

## 🛑 Pod Deletion & Graceful Shutdown

When a Pod is deleted:

1. Kubernetes sends `SIGTERM`
2. Application gets time to shutdown gracefully
3. After timeout, Kubernetes sends `SIGKILL`

This helps applications:

* finish requests
* close DB connections
* save data safely

### ⚙️ Grace Period Configuration

You can control shutdown timeout using:

```yaml
terminationGracePeriodSeconds: 30
```

Meaning:

* Kubernetes waits 30 seconds before forcefully killing container

---

## 🛠️ How to Debug Pod Lifecycle Issues

Most useful troubleshooting commands:

---

## Check Pod Status

```bash
kubectl get pods
```

---

## Describe Pod

```bash
kubectl describe pod <pod-name>
```

Shows:

* events
* scheduling issues
* restart reasons
* probe failures

---

## Check Logs

```bash
kubectl logs <pod-name>
```

---

## Check Previous Crashed Container Logs

```bash
kubectl logs <pod-name> -p
```

Very useful for:

* CrashLoopBackOff troubleshooting

---

# 🔑 Interview One-Liners

| Topic            | Interview Answer                                      |
| ---------------- | ----------------------------------------------------- |
| Pending          | Pod accepted but not fully running                    |
| Running          | Containers are running                                |
| CrashLoopBackOff | Container repeatedly crashes and restarts             |
| Init Container   | Special container that runs before main app container |
| Succeeded        | Containers completed successfully                     |

---

# ⚠️ Common Beginner Mistakes

* ❌ Assuming `Running` means application is healthy
* ❌ Ignoring `kubectl describe pod` events
* ❌ Confusing Pod phase with container state
* ❌ Not checking previous container logs
* ❌ Ignoring probe failures

---

# ✅ Key Takeaways

* Pods move through multiple lifecycle phases
* Pod phase and container state are different
* `CrashLoopBackOff` is a very common production issue
* Init containers run before application containers
* Events and logs are the most important debugging tools

---
