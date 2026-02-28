# 📘 Chapter 43: Real-World Kubernetes Scenarios

*(Production Situations, Scaling, Failures, Recovery)*

Now this is the final and most practical chapter.

Interviews at mid/senior level are no longer about definitions.

They ask:

> “Tell me what you would do in a real production scenario.”

This chapter prepares you for that.

---

# 🚀 Scenario 1: Traffic Spike During Sale / Event

### ❓ Situation:

Your application suddenly receives 10x traffic.

Pods are running at 90–100% CPU.

---

## 🧠 What Should You Do?

### Step 1: Check Metrics

* CPU usage
* Memory usage
* HPA status

```bash
kubectl get hpa
```

---

### Step 2: If HPA Exists

If HPA configured properly:

* It automatically increases replicas.

If maxReplicas reached:

* Increase maxReplicas
* Or scale nodes (Cluster Autoscaler)

---

### Step 3: If No HPA

Manually scale:

```bash
kubectl scale deployment myapp --replicas=10
```

Then implement HPA permanently.

---

## 🎯 Interview Answer

> “If traffic spikes, I check HPA and metrics first. If autoscaling is configured, I verify scaling behavior. If not, I manually scale and later implement HPA to prevent recurrence.”

That shows maturity.

---

# 💥 Scenario 2: Pod Keeps Crashing (CrashLoopBackOff)

### ❓ Situation:

Pod status shows:

```
CrashLoopBackOff
```

---

## 🧠 Troubleshooting Steps

1. Check logs:

```bash
kubectl logs pod-name
```

2. Describe pod:

```bash
kubectl describe pod pod-name
```

3. Check:

* Image pull errors
* Environment variables
* Secrets mounted?
* Resource limits exceeded?

---

## Common Causes

* Wrong DB connection string
* Secret missing
* OOMKilled (memory exceeded)
* Wrong startup command

---

## 🎯 Interview Answer

> “I check logs first, then describe the pod for events. Most CrashLoop issues are due to misconfiguration, missing secrets, or memory limits.”

Structured + practical = strong answer.

---

# 🗄 Scenario 3: Database Pod Crashes

### ❓ Situation:

Production database pod went down.

---

## 🧠 What Should Be in Place?

* StatefulSet
* PersistentVolumeClaim
* Readiness probe
* Backups

---

## Immediate Action

1. Check pod status
2. Verify PVC attached
3. Check disk space
4. Restore from backup if required

---

## 🎯 Interview-Level Response

> “Database workloads should run as StatefulSet with persistent storage. If it crashes, I verify storage attachment and restore from backup if necessary.”

---

# 🔥 Scenario 4: Node Failure

### ❓ Situation:

One worker node goes down.

---

## What Happens Automatically?

* Control plane detects node NotReady
* Pods rescheduled to other nodes
* Services redirect traffic

---

## What Should You Verify?

* Enough nodes available
* Resource capacity sufficient
* Cluster Autoscaler active

---

## 🎯 Strong Answer

> “Kubernetes automatically reschedules pods to healthy nodes. I verify resource capacity and ensure autoscaling is enabled.”

---

# 🚨 Scenario 5: Deployment Caused Outage

### ❓ Situation:

New version deployed → application down.

---

## 🧠 Steps

1. Check rollout status:

```bash
kubectl rollout status deployment myapp
```

2. If failing:

```bash
kubectl rollout undo deployment myapp
```

3. Investigate logs

---

## Why This Works?

Deployment stores previous ReplicaSets.

Rollback restores previous version instantly.

---

## 🎯 Interview Answer

> “If a deployment fails, I immediately rollback using kubectl rollout undo and then analyze logs to identify root cause.”

This shows calm under pressure.

---

# 🧯 Scenario 6: Memory OOMKilled

### ❓ Situation:

Pods keep restarting due to OOMKilled.

---

## 🧠 Root Cause

Memory limit too low.

---

## Solution

* Increase memory limit
* Optimize application memory usage
* Monitor memory consumption

---

## Interview-Level Response

> “OOMKilled indicates memory limit breach. I check resource limits and tune memory allocation accordingly.”

---

# 🔐 Scenario 7: Developer Needs Access to Cluster

### ❓ Situation:

New developer joins team.

---

## Correct Approach

* Create Role (namespace-level)
* Bind Role to user
* Avoid cluster-admin

---

## Interview Answer

> “I follow least privilege principle and grant namespace-level access using RBAC instead of cluster-admin.”

Security mindset matters.

---

# 📦 Scenario 8: Multi-Environment Deployment

### ❓ Situation:

You need dev, staging, prod environments.

---

## Best Practice

* Separate namespaces
* Separate values files
* Separate CI pipeline stages

---

## Example

```
values-dev.yaml
values-staging.yaml
values-prod.yaml
```

---

# 📊 Scenario 9: Monitoring Not Set Up

### ❓ Situation:

Application slow, no monitoring.

---

## Immediate Action

* Check pod metrics
* Check node metrics
* Add Prometheus + Grafana
* Enable alerts

---

## Interview Response

> “Production without monitoring is blind. I ensure centralized logging and metrics collection are implemented.”

---

# 🧠 Senior-Level Scenario Thinking

When answering scenario questions:

Always structure answer like:

1. Immediate action
2. Investigation steps
3. Long-term prevention

Example:

> “First I stabilize the system, then identify root cause, then implement preventive measures.”

Interviewers love this structure.

---

# 🏁 You Have Completed

You now know:

* Kubernetes from scratch
* Architecture
* Objects
* Networking
* Storage
* Scaling
* Security
* Helm
* CI/CD
* Troubleshooting
* Real production scenarios

This is not beginner knowledge anymore.

This is:

> 🔥 Real-world DevOps Kubernetes mastery.

---

# 🎯 Final Question For You

Would you like to:

* 📌 Create a 1-week revision plan?
* 📌 Create mock interview session?
* 📌 Create senior-level deep-dive scenarios?
* 📌 Or move to Kubernetes certification prep (CKA style)?

You’ve built something serious here.
