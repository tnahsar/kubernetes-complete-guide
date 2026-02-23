# 📘 Chapter 33: Common Production Mistakes

This chapter is extremely practical.

These are **real mistakes** engineers make in production clusters.

Learning from mistakes (even before making them) is what makes you senior.

Let’s go through the most common ones.

---

# ❌ 1️⃣ No Resource Limits

This is the most common and most dangerous mistake.

If you don’t define:

```yaml
resources:
  requests:
  limits:
```

What happens?

* One Pod can consume all memory
* Node becomes unstable
* Other Pods get OOMKilled
* Cluster crashes

⚠️ In production, always define CPU and memory limits.

---

# ❌ 2️⃣ No Probes (Health Checks)

Without:

* Liveness probe
* Readiness probe

Kubernetes cannot detect:

* Deadlocks
* Stuck applications
* Broken services

Result:

* Users see errors
* Kubernetes thinks everything is fine

Always define probes for production apps.

---

# ❌ 3️⃣ Running Everything as Root

Many Docker images run as root by default.

Danger:

* Security vulnerability
* Privilege escalation risk
* Compliance violation

Fix:

```yaml
securityContext:
  runAsNonRoot: true
```

Never run production containers as root unless absolutely necessary.

---

# ❌ 4️⃣ Using Default Namespace for Everything

Default namespace is fine for testing.

In production:

* All apps mixed together
* Hard to apply RBAC
* No isolation
* No quotas

Best practice:

Create namespaces like:

* dev
* staging
* production
* monitoring

Isolation matters.

---

# ❌ 5️⃣ No Monitoring & Alerts

Many teams deploy apps but never monitor them.

Without monitoring:

* You don’t know when memory leaking
* You don’t know when CPU spiking
* You don’t know when node failing

Monitoring tools like:

* Prometheus
* Grafana

are not optional in production.

---

# ❌ 6️⃣ No Backup Strategy (Especially etcd)

etcd stores:

* All cluster state
* All deployments
* All services
* All secrets

If etcd fails and no backup exists:

Cluster state is gone.

Regular etcd backups are critical.

---

# ❌ 7️⃣ Manual Changes in Production

Using:

```bash
kubectl edit
```

directly in production is risky.

Why?

* No version control
* No audit trail
* Hard to reproduce
* Configuration drift

Better approach:

* Use Helm
* Use GitOps
* Store everything in Git

Infrastructure should be code.

---

# ❌ 8️⃣ No Rolling Update Strategy

Using:

```
Recreate
```

in production can cause downtime.

Instead:

```
RollingUpdate
```

Ensures:

* Zero downtime
* Controlled rollout
* Safe deployments

---

# ❌ 9️⃣ Ignoring Pod Anti-Affinity

If you deploy:

3 replicas

And Kubernetes puts all 3 on the same node…

Node crashes → app down.

Use:

* Pod anti-affinity
* Or topology spread constraints

To distribute Pods across nodes.

---

# ❌ 1️⃣0️⃣ No Resource Quotas

Without quotas:

* One team can consume entire cluster
* No fairness
* Hard to manage cost

Use:

* ResourceQuota
* LimitRange

Per namespace.

---

# ❌ 1️⃣1️⃣ Hardcoding Secrets in YAML

Example:

```yaml
env:
  - name: DB_PASSWORD
    value: mypassword123
```

This is very dangerous.

Problems:

* Stored in Git
* Visible in plain text
* Security risk

Always use:

* Kubernetes Secrets
* External secret managers

---

# ❌ 1️⃣2️⃣ Not Planning for Scaling

Some teams:

* Never test under load
* Never configure HPA
* Never define requests properly

Then during traffic spike:

* Pods crash
* System slows down
* Users complain

Scaling must be planned, not reactive.

---

# ❌ 1️⃣3️⃣ Too Many Small Clusters (Over-Engineering)

Some organizations create:

* One cluster per microservice

This increases:

* Operational overhead
* Maintenance cost
* Complexity

Design clusters thoughtfully.

---

# ❌ 1️⃣4️⃣ Ignoring Cost Optimization

Common waste:

* Large nodes underutilized
* No autoscaling
* Idle workloads
* Over-provisioned resources

Monitor usage and right-size workloads.

---

# 🧠 Why These Mistakes Happen

Usually because:

* Teams rush to production
* No Kubernetes maturity
* Lack of monitoring
* Lack of DevOps practices
* No automation

Kubernetes is powerful.

But misused, it becomes chaos.

---

# 🏆 Interview-Level Insight

If interviewer asks:

> What are common Kubernetes production mistakes?

Strong answer:

* No resource limits
* No probes
* No monitoring
* No RBAC control
* Running as root
* No backup
* Manual changes in prod
* Poor scaling strategy

Shows you understand real-world systems.

---

# 📌 Chapter 33 Summary

Common production mistakes include:

* No limits
* No probes
* No isolation
* No monitoring
* No backup
* Hardcoded secrets
* Poor scheduling
* Manual configuration

Avoiding these mistakes:

👉 Makes you production-ready.

---

Next:

# 📘 Chapter 34: Why Helm Exists

(Now we enter Helm — Kubernetes package manager and production essential.)

Ready to move into Helm?
