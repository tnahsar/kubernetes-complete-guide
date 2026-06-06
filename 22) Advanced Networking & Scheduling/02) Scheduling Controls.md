# 📘 Chapter 29: Scheduling Controls

Now we are entering **smart cluster management** territory.

Until now, Kubernetes was automatically placing Pods on nodes for us.

But in real production, sometimes you need to control:

* ❓ Which node runs which Pod
* ❓ Which Pods should NOT run together
* ❓ Which nodes are reserved for special workloads
* ❓ How many Pods must stay available during maintenance

This chapter explains how to control scheduling like a pro.

---

# 🧠 First: How Scheduling Normally Works

Remember from Chapter 5:

The **Scheduler** (part of control plane) decides:

> "On which node should this Pod run?"

By default, it checks:

* CPU availability
* Memory availability
* Taints
* Constraints

Then places the Pod on the “best” node.

But sometimes default behavior is not enough.

That’s where scheduling controls come in.

---

# 🏷 1️⃣ Node Selectors (Basic Control)

## 📌 What Is It?

A simple way to tell Kubernetes:

> "Run this Pod only on nodes with this label."

---

## 🔹 Step 1: Label a Node

```bash
kubectl label nodes node1 disktype=ssd
```

Now node1 has:

```
disktype=ssd
```

---

## 🔹 Step 2: Use nodeSelector in Pod

```yaml
spec:
  nodeSelector:
    disktype: ssd
```

Now the Pod will:

* Only run on nodes labeled `disktype=ssd`
* Fail if none available

---

## 🧠 When To Use

* GPU nodes
* SSD storage nodes
* Dedicated production nodes
* High-memory nodes

---

## 🚨 Limitation

NodeSelector is:

* Simple
* Hard requirement
* Not flexible

For advanced logic → use Affinity (coming soon 👇)

---

# 🚫 2️⃣ Taints & Tolerations (Node Protection System)

This is opposite of nodeSelector.

Instead of:

> Pod chooses node

Here:

> Node rejects Pods

---

## 🧠 Mental Model

Taint = “No one allowed here unless special permission.”

---

## 🔹 Add Taint to Node

```bash
kubectl taint nodes node1 key=value:NoSchedule
```

Now:

* No Pods will schedule on node1
* Unless they tolerate it

---

## 🔹 Add Toleration to Pod

```yaml
spec:
  tolerations:
  - key: "key"
    operator: "Equal"
    value: "value"
    effect: "NoSchedule"
```

Now this Pod:

* Is allowed on that node

---

## 🧠 Effects of Taints

| Effect           | Meaning                 |
| ---------------- | ----------------------- |
| NoSchedule       | Don’t schedule new Pods |
| PreferNoSchedule | Avoid if possible       |
| NoExecute        | Remove running Pods     |

---

## 🏭 Real Use Cases

* Dedicated nodes for database
* GPU nodes
* System nodes
* Maintenance mode

Very powerful in production.

---

# 🎯 3️⃣ Affinity & Anti-Affinity (Advanced Placement Rules)

This is advanced version of nodeSelector.

Two types:

### 🟢 Node Affinity

Control which nodes Pods can run on.

### 🔵 Pod Affinity

Control which Pods run together.

### 🔴 Pod Anti-Affinity

Prevent Pods from running together.

---

## 🟢 Node Affinity

More expressive than nodeSelector.

You can say:

* Must match
* Prefer to match
* Complex conditions

Example:

```yaml
affinity:
  nodeAffinity:
    requiredDuringSchedulingIgnoredDuringExecution:
      nodeSelectorTerms:
      - matchExpressions:
        - key: disktype
          operator: In
          values:
          - ssd
```

Now:

* Only schedule on SSD nodes
* More flexible logic possible

---

## 🔵 Pod Affinity

Example:

> Place frontend Pod near backend Pod (same node or zone)

Useful for:

* Low latency communication

---

## 🔴 Pod Anti-Affinity (Very Important)

Example:

> Never place 2 replicas of same app on same node.

Why?

If node crashes → you lose all replicas 😱

Anti-affinity spreads Pods across nodes.

Production essential.

---

# 🛑 4️⃣ Pod Disruption Budgets (PDB)

Now this is about **availability during maintenance**.

Imagine:

You have 5 replicas.

Cluster admin upgrades nodes.

Some Pods must be evicted.

If all Pods stop → downtime 😬

PDB prevents that.

---

## 🧠 What Is PDB?

It defines:

> Minimum number of Pods that must stay available.

---

## Example:

```yaml
apiVersion: policy/v1
kind: PodDisruptionBudget
spec:
  minAvailable: 2
```

Meaning:

* At least 2 Pods must stay running
* Kubernetes will not allow more disruptions

---

## 🏭 When Used?

* During node upgrades
* During cluster scaling
* During voluntary disruptions

Important:

PDB does NOT protect from crashes.
It protects from planned disruptions.

---

# 🧠 How All Scheduling Controls Work Together

Order of evaluation:

1. Node resources check
2. Taints
3. NodeSelector
4. Affinity rules
5. PDB during eviction

Everything combined controls Pod placement.

---

# 🏆 Real Production Example

Imagine:

* 3 nodes
* 3 replicas
* Anti-affinity enabled
* PDB minAvailable=2

Result:

* Pods spread across nodes
* During upgrade, only 1 can go down
* App remains available

That’s production-grade reliability.

---

# 🚨 Common Beginner Mistakes

❌ Overusing hard rules
❌ Forgetting anti-affinity
❌ Using only nodeSelector in production
❌ No PDB defined
❌ Blocking scheduling accidentally with taints

---

# 📌 Chapter 29 Summary

Scheduling Controls allow you to:

* Control where Pods run
* Protect special nodes
* Spread Pods safely
* Maintain high availability
* Handle maintenance safely

Tools covered:

* NodeSelector (basic filtering)
* Taints & Tolerations (node protection)
* Affinity & Anti-Affinity (advanced logic)
* Pod Disruption Budgets (availability control)

---

Next we move to:

# 📘 Chapter 30: Logging & Monitoring Concepts

(Where we finally learn how to observe production clusters 👀📊)

Ready to continue?
