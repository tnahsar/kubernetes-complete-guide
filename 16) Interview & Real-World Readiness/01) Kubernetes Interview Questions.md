# 📘 Chapter 42: Kubernetes Interview Questions (Real Answers)

Now we enter **interview mode**.

You already understand Kubernetes deeply.
This chapter will help you:

> 🎯 Convert knowledge → Clear, confident interview answers

These are real questions asked in DevOps / SRE / Kubernetes interviews.

---

# 🔹 1️⃣ Pod vs Deployment

### ❓ Question:

What is the difference between a Pod and a Deployment?

### ✅ Strong Answer:

> A Pod is the smallest deployable unit in Kubernetes that runs one or more containers.
> A Deployment manages Pods and ensures desired state, supports rolling updates, rollbacks, and scaling.

### 💡 Short Version:

| Pod             | Deployment   |
| --------------- | ------------ |
| Runs containers | Manages Pods |
| No self-healing | Self-healing |
| Not scalable    | Scalable     |

🔥 Bonus line:

> In production, we rarely create Pods directly. We use Deployments.

---

# 🔹 2️⃣ What Happens When You Run `kubectl apply`?

### ❓ Question:

Explain what happens internally when you apply a Deployment YAML.

### ✅ Answer:

> The YAML is sent to the API Server.
> The API Server stores desired state in etcd.
> The Scheduler assigns Pods to nodes.
> The kubelet pulls the container image and runs it.

This shows architecture understanding.

---

# 🔹 3️⃣ Service Types

### ❓ Question:

What are different types of Services?

### ✅ Answer:

| Type         | Use Case                      |
| ------------ | ----------------------------- |
| ClusterIP    | Internal communication        |
| NodePort     | Expose via node IP            |
| LoadBalancer | External traffic via cloud LB |

🔥 Strong Interview Line:

> ClusterIP is default and used for internal service-to-service communication.

---

# 🔹 4️⃣ ConfigMap vs Secret

### ❓ Question:

What is difference between ConfigMap and Secret?

### ✅ Answer:

| ConfigMap          | Secret            |
| ------------------ | ----------------- |
| Non-sensitive data | Sensitive data    |
| Plain text         | Base64 encoded    |
| App config         | Passwords, tokens |

🔥 Bonus line:

> In production, secrets should be encrypted at rest and ideally managed externally.

---

# 🔹 5️⃣ Liveness vs Readiness Probe

### ❓ Question:

Difference between liveness and readiness probe?

### ✅ Answer:

| Liveness           | Readiness                |
| ------------------ | ------------------------ |
| Restarts container | Controls traffic routing |
| Detects dead app   | Detects not-ready app    |

Strong example:

> If DB connection fails temporarily, readiness fails but container doesn’t restart.

---

# 🔹 6️⃣ What Is HPA?

### ❓ Question:

How does Horizontal Pod Autoscaler work?

### ✅ Answer:

> HPA automatically scales the number of pods based on CPU or memory usage.
> It uses Metrics Server to fetch resource metrics and adjusts replicas dynamically.

🔥 Bonus:

> We always define minReplicas > 1 in production.

---

# 🔹 7️⃣ StatefulSet vs Deployment

### ❓ Question:

When do you use StatefulSet?

### ✅ Answer:

> StatefulSet is used for stateful applications like databases.
> It provides stable pod names, persistent storage, and ordered deployment.

Deployment = stateless
StatefulSet = stateful

---

# 🔹 8️⃣ How Does Kubernetes Networking Work?

### ❓ Question:

Explain Kubernetes networking model.

### ✅ Answer:

> Kubernetes follows a flat network model where every Pod gets a unique IP, and all Pods can communicate with each other without NAT.

Strong bonus:

> This is implemented using CNI plugins like Calico or Flannel.

---

# 🔹 9️⃣ What Is RBAC?

### ❓ Question:

Explain RBAC in Kubernetes.

### ✅ Answer:

> RBAC controls access to Kubernetes resources using Roles and RoleBindings.
> It follows least privilege principle.

Strong example:

> Developers get namespace-level Role, not cluster-admin.

---

# 🔹 🔟 How Do You Troubleshoot a Pod?

### ❓ Question:

Pod is not running. What will you check?

### ✅ Structured Answer:

1. `kubectl get pods`
2. Check pod status
3. `kubectl describe pod`
4. Check events
5. `kubectl logs`
6. Check resource limits
7. Check image pull errors

This structured answer impresses interviewers.

---

# 🔹 1️⃣1️⃣ What Is Helm?

### ❓ Question:

Why use Helm?

### ✅ Answer:

> Helm is a Kubernetes package manager that helps manage reusable and versioned application deployments using templated YAML files.

Bonus:

> It simplifies environment-specific configuration and supports upgrade & rollback.

---

# 🔹 1️⃣2️⃣ Rolling Update vs Recreate

### ❓ Question:

What is rolling update?

### ✅ Answer:

> Rolling update gradually replaces old pods with new ones without downtime.
> It ensures application availability during deployment.

---

# 🔹 1️⃣3️⃣ What Happens If a Node Fails?

### ✅ Answer:

> The control plane detects node failure.
> Pods running on that node are rescheduled to healthy nodes.

Shows understanding of self-healing.

---

# 🔹 1️⃣4️⃣ Why Not Run Containers as Root?

### ✅ Answer:

> Running as root increases security risk.
> If container is compromised, attacker gains root-level access.

Production security mindset.

---

# 🔹 1️⃣5️⃣ What Is etcd?

### ✅ Answer:

> etcd is a distributed key-value store that stores cluster state and configuration.

Bonus:

> If etcd is lost, cluster state is lost.

---

# 🎯 How to Answer Like a Pro

During interviews:

* Keep answers structured
* Use tables when comparing
* Add 1 production-level line
* Avoid over-explaining

Confidence + clarity > long explanation

---

# 🧠 Final Interview Mindset

If you can confidently explain:

* Pod lifecycle
* Deployment strategy
* Services
* Storage
* Scaling
* Security
* Helm
* Troubleshooting

You are strong for:

* DevOps Engineer
* Kubernetes Engineer
* SRE roles

---

# 🚀 Final Chapter Left

📘 **Chapter 43: Real-World Kubernetes Scenarios**

* Traffic spike handling
* Production outage
* Database crash
* Node failure
* Deployment gone wrong

This will prepare you for scenario-based interviews.

Shall we finish the journey with Chapter 43?
