# 📘 Chapter 36: Helm Architecture (Helm v3)

Now that you understand:

* Why Helm exists
* What Chart, Release, Repository mean

Let’s understand:

> How Helm actually works internally.

This chapter is important for interviews and production understanding.

---

# 🧠 Big Picture: Helm Architecture (v3)

In **Helm v3**, architecture is very simple.

There are only 2 main components:

1️⃣ Helm CLI
2️⃣ Kubernetes API Server

That’s it.

No extra server inside cluster.

---

# 🏗 Visual Flow (Helm v3)

```
You (CLI)
   ↓
Helm CLI
   ↓
Kubernetes API Server
   ↓
Cluster Resources Created
```

Helm is just a smart client tool.

---

# 🧩 1️⃣ Helm CLI

This is the command-line tool you install on your machine.

Examples:

```bash
helm install
helm upgrade
helm rollback
helm uninstall
```

Helm CLI does:

* Reads chart files
* Reads values.yaml
* Renders templates
* Converts templates into pure Kubernetes YAML
* Sends final YAML to Kubernetes API

It does NOT:

* Run continuously
* Stay inside cluster
* Act as controller

It only runs when you execute commands.

---

# 🧩 2️⃣ Kubernetes API Server

From Chapter 5, remember:

The API Server is the “front door” of Kubernetes.

Helm communicates directly with:

> Kubernetes API Server

Just like kubectl does.

So technically:

Helm = Smart kubectl with templating and release tracking.

---

# 🔐 Where Does Helm Store Release Information?

Very important question.

When you install a release:

```bash
helm install myapp mychart
```

Helm stores release metadata inside the cluster as:

> Kubernetes Secrets

These Secrets contain:

* Release name
* Chart version
* Configuration values
* Revision history

That is how:

```bash
helm history myapp
```

works.

---

# 🔄 How Upgrade Works Internally

When you run:

```bash
helm upgrade myapp mychart
```

Helm:

1. Reads new chart
2. Renders new templates
3. Compares with existing release
4. Sends updated manifests to API Server
5. Stores new revision in Secret

Now release revision increases:

* Revision 1
* Revision 2
* Revision 3

---

# 🔙 How Rollback Works

When you run:

```bash
helm rollback myapp 1
```

Helm:

1. Fetches revision 1 from stored Secrets
2. Re-applies those manifests
3. Creates new revision entry

Very clean and safe.

---

# 🚫 What Changed From Helm v2?

In older Helm v2:

There was a component called:

> Tiller

Tiller:

* Ran inside cluster
* Had high permissions
* Was security risk
* Managed releases server-side

In Helm v3:

❌ No Tiller
✅ Direct API communication
✅ Simpler
✅ More secure

Modern Kubernetes always uses Helm v3.

---

# 🧠 Helm Rendering Process (Important Concept)

Before sending to Kubernetes, Helm:

1. Reads templates
2. Replaces variables with values
3. Generates final YAML

Example:

Template:

```yaml
replicas: {{ .Values.replicaCount }}
```

values.yaml:

```yaml
replicaCount: 3
```

Final rendered YAML sent to Kubernetes:

```yaml
replicas: 3
```

Helm acts like a template engine.

---

# 🏭 Real Production Flow

CI/CD Pipeline:

```
Developer pushes code
      ↓
Docker image built
      ↓
CI pipeline runs helm upgrade
      ↓
Helm renders chart
      ↓
Kubernetes updates deployment
      ↓
Rolling update happens
```

Helm fits perfectly in automation.

---

# 🔒 Security Perspective

Helm uses:

* Your kubeconfig
* Your RBAC permissions

If you don’t have permission to create resources,
Helm will fail.

Helm does NOT bypass security.

---

# 📊 Helm vs kubectl Architecture

| kubectl             | Helm                     |
| ------------------- | ------------------------ |
| Applies raw YAML    | Applies templated YAML   |
| No version tracking | Tracks release revisions |
| No packaging        | Uses charts              |
| No rollback history | Built-in rollback        |

Helm is basically a higher-level deployment tool.

---

# 🧠 Simple Mental Model

Helm is:

> A client-side templating engine + release manager that talks directly to Kubernetes API.

Nothing magical inside cluster.

Everything goes through the API Server.

---

# 📌 Chapter 36 Summary

Helm v3 architecture:

* No Tiller
* Only Helm CLI
* Talks directly to Kubernetes API
* Stores release history as Secrets
* Supports upgrade & rollback
* Uses template rendering

Helm is:

> Smart Kubernetes deployment automation tool.

---

Next:

# 📘 Chapter 37: Helm Chart Structure (Line-by-Line)

(This is where we open a chart folder and understand every file clearly.)

Ready to continue?
