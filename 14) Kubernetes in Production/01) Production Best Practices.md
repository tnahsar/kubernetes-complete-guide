# 📘 Chapter 32: Production Best Practices

Now we enter **real-world Kubernetes**.

Running Kubernetes locally is easy.
Running it in production is serious business.

In production, you care about:

* Stability
* Security
* Scalability
* Cost
* Isolation
* Maintainability

This chapter teaches you how professionals run Kubernetes clusters.

---

# 🧠 1️⃣ Always Set Resource Requests & Limits

This is one of the **most important rules**.

If you don’t set resource limits:

❌ One Pod can consume all memory
❌ Node can crash
❌ Other Pods get killed
❌ Cluster becomes unstable

---

## ✅ Resource Requests

Defines:

> Minimum resources needed

Example:

```yaml
resources:
  requests:
    cpu: "200m"
    memory: "256Mi"
```

Scheduler uses this to decide where to place Pod.

---

## ✅ Resource Limits

Defines:

> Maximum resources allowed

```yaml
resources:
  limits:
    cpu: "500m"
    memory: "512Mi"
```

If memory exceeds limit → Pod is killed (OOMKilled).

---

## 🏭 Production Rule

Always define:

* CPU request
* Memory request
* CPU limit
* Memory limit

Never run unlimited containers in production.

---

# 🏷 2️⃣ Use Namespaces Properly

Namespaces provide:

> Logical isolation inside cluster.

Example environments:

* dev
* staging
* production
* monitoring

Create namespace:

```bash
kubectl create namespace production
```

---

## 🧠 Why Namespaces Matter

Without namespaces:

❌ Everything mixed together
❌ Hard to manage
❌ Hard to apply security
❌ Hard to set quotas

With namespaces:

✅ Isolation
✅ Better organization
✅ Per-namespace RBAC
✅ Resource quotas

---

# 🔒 3️⃣ Environment Separation Strategy

In production setups, companies often use:

### Option 1: Separate Namespaces

* dev
* staging
* prod

### Option 2: Separate Clusters (Recommended for large companies)

* Dev cluster
* Staging cluster
* Production cluster

Why?

Production must be:

* Highly secure
* Highly available
* Strictly controlled

---

# 🔐 4️⃣ Security Best Practices

## 🚫 Don’t Run Containers as Root

In Pod spec:

```yaml
securityContext:
  runAsNonRoot: true
```

Running as root:

* Security risk
* Privilege escalation risk

---

## 🔐 Use RBAC Strictly

Give:

* Minimum permissions
* Least privilege access

Never give:

```
cluster-admin
```

to everyone.

---

## 🔒 Use Network Policies

Restrict Pod communication.

Example:

* Frontend can talk to backend
* Backend cannot talk to database directly
* Only specific Pods communicate

This prevents lateral movement in attacks.

---

# 📊 5️⃣ Use Probes Always

Never deploy app without:

* Liveness probe
* Readiness probe

Why?

Without probes:

* Kubernetes doesn’t know if app is broken.

With probes:

* Kubernetes restarts unhealthy containers
* Removes broken Pods from load balancer

---

# 🔄 6️⃣ Enable Rolling Updates

Never use:

```
Recreate strategy
```

In production, use:

```
RollingUpdate
```

Ensures:

* Zero downtime
* Safe deployments
* Controlled rollout

---

# 📦 7️⃣ Use Helm or GitOps for Deployments

Manual kubectl apply in production is dangerous.

Better approaches:

* Helm charts
* GitOps tools

Example tools:

* ArgoCD
* Flux

Infrastructure should be:

> Version controlled and reproducible.

---

# 📈 8️⃣ Enable Monitoring & Alerts

Production must have:

* Prometheus
* Grafana
* Alerting system

You should know:

* CPU usage
* Memory usage
* Pod restarts
* Node health

Without monitoring → blind production.

---

# 🗄 9️⃣ Backup etcd Regularly

Remember from Chapter 5:

etcd stores:

> Entire cluster state

If etcd is lost:

* Cluster is gone.

Regular backups are critical.

---

# 🔄 1️⃣0️⃣ Use Pod Disruption Budgets

For high availability apps:

* Prevent too many Pods being evicted
* Maintain minimum availability

Essential during:

* Node upgrades
* Maintenance

---

# 🧠 1️⃣1️⃣ Use Labels Consistently

Bad labeling = chaos.

Good labeling example:

```
app: payment
env: production
tier: backend
version: v1
```

Labels help with:

* Service selection
* Monitoring
* Filtering
* Automation

---

# 💰 1️⃣2️⃣ Control Resource Usage (Cost Optimization)

Production clusters cost money.

Best practices:

* Remove unused resources
* Use autoscaling
* Right-size Pods
* Monitor idle workloads

---

# 🏗 Real Production Setup Example

A mature company setup:

* Separate prod cluster
* Namespace per team
* Resource limits enforced
* RBAC strict
* Network policies applied
* Monitoring enabled
* Logging centralized
* Helm-based deployments
* GitOps workflow
* etcd backups scheduled

That’s professional-grade Kubernetes.

---

# 🚨 Common Production Mistakes

❌ No resource limits
❌ No probes
❌ Everything in default namespace
❌ Running as root
❌ No monitoring
❌ No backups
❌ Manual kubectl changes in prod
❌ No scaling strategy

---

# 🏆 Interview-Level Answer

If asked:

> What are Kubernetes production best practices?

Strong answer includes:

* Resource limits
* Namespaces
* RBAC
* Probes
* Rolling updates
* Monitoring
* Backup etcd
* GitOps / Helm
* Network policies

That shows maturity.

---

# 📌 Chapter 32 Summary

Production Kubernetes requires:

* Resource control
* Isolation
* Security
* Observability
* Automation
* Backup strategy

Kubernetes is powerful.

But without best practices → it becomes chaos.

---

Next:

# 📘 Chapter 33: Common Production Mistakes

(This one is very practical — things people actually mess up in real life.)

Ready to continue?
