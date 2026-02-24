# 📘 Chapter 38: Using Helm Charts

Now that you understand:

* What Helm is
* Chart structure
* Release concept
* Helm architecture

It’s time to actually **use Helm like a DevOps engineer**.

This chapter is practical and command-focused.

---

# 🧠 Basic Helm Workflow

In real life, Helm usage follows this flow:

1️⃣ Add repository
2️⃣ Search chart
3️⃣ Install chart
4️⃣ Upgrade chart
5️⃣ Rollback if needed
6️⃣ Uninstall when done

Let’s go step-by-step.

---

# 🏪 1️⃣ Add a Helm Repository

A repository stores Helm charts.

Example:

```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
```

Then update repo list:

```bash
helm repo update
```

Now Helm knows where to download charts from.

---

# 🔍 2️⃣ Search for Charts

Search inside repo:

```bash
helm search repo nginx
```

You will see:

* Chart name
* Chart version
* App version
* Description

This helps you choose correct chart.

---

# 📦 3️⃣ Install a Chart

Basic syntax:

```bash
helm install <release-name> <chart-name>
```

Example:

```bash
helm install mynginx bitnami/nginx
```

What happens?

* Chart downloaded
* Templates rendered
* Kubernetes resources created
* Release stored

Check installed releases:

```bash
helm list
```

---

# ⚙️ 4️⃣ Install with Custom Values

Instead of default values, use custom file:

```bash
helm install myapp ./mychart -f values-prod.yaml
```

Or override directly:

```bash
helm install myapp ./mychart --set replicaCount=3
```

But in production:

✅ Prefer values files
❌ Avoid too many --set flags

---

# 🔄 5️⃣ Upgrade a Release

If you update:

* Image version
* Replica count
* Configuration

Use:

```bash
helm upgrade myapp ./mychart
```

Or with new values:

```bash
helm upgrade myapp ./mychart -f values-prod.yaml
```

Helm will:

* Compare old and new
* Apply changes
* Create new revision

---

# 📜 6️⃣ Check Release History

See all revisions:

```bash
helm history myapp
```

Output example:

| Revision | Status   | Chart Version |
| -------- | -------- | ------------- |
| 1        | deployed | 0.1.0         |
| 2        | deployed | 0.1.1         |

Each upgrade creates new revision.

---

# 🔙 7️⃣ Rollback a Release

If something breaks:

```bash
helm rollback myapp 1
```

This restores revision 1.

Helm re-applies previous configuration.

This is production lifesaver.

---

# 🗑 8️⃣ Uninstall a Release

Remove everything created by Helm:

```bash
helm uninstall myapp
```

Helm deletes:

* Deployment
* Service
* ConfigMaps
* Secrets
* Everything created by release

Clean and simple.

---

# 🧪 9️⃣ Dry Run (Very Important)

Before installing in production:

```bash
helm install myapp ./mychart --dry-run --debug
```

This shows:

* Rendered YAML
* Final output
* No resources created

Very useful for testing.

---

# 🔍 1️⃣0️⃣ Template Rendering Only

To just see generated YAML:

```bash
helm template myapp ./mychart
```

Helm prints final YAML to console.

Good for debugging.

---

# 📊 Real Production Deployment Flow

In CI/CD pipeline:

```
1. Code pushed
2. Docker image built
3. Image pushed to registry
4. Helm upgrade command executed
5. Kubernetes performs rolling update
6. Monitoring checks health
```

Helm integrates naturally in automation.

---

# 🧠 Best Practices While Using Helm

✅ Always use versioned charts
✅ Maintain separate values files
✅ Use dry-run before production
✅ Use rollback if deployment fails
✅ Store charts in Git
✅ Avoid manual kubectl edits after Helm deployment

Never mix manual edits with Helm-managed resources.

---

# 🚨 Common Mistakes

❌ Using --set too much
❌ Forgetting to update helm repo
❌ Editing deployed resources manually
❌ Not checking helm history
❌ Not testing with dry-run

---

# 🏆 Interview-Level Commands You Must Know

Be comfortable with:

```bash
helm install
helm upgrade
helm rollback
helm uninstall
helm list
helm history
helm template
helm repo add
```

These are essential.

---

# 📌 Chapter 38 Summary

Using Helm includes:

* Adding repositories
* Installing charts
* Customizing values
* Upgrading releases
* Rolling back safely
* Uninstalling cleanly
* Testing with dry-run

Helm makes Kubernetes deployments:

* Reproducible
* Versioned
* Safe
* Production-ready

---

Next:

# 📘 Chapter 39: Writing Your Own Helm Chart

(This is where you convert raw YAML into a reusable Helm chart.)

Ready to continue?
