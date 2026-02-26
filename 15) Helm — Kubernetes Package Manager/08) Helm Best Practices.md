# 📘 Chapter 41: Helm Best Practices

*(Values Management, Chart Versioning, Real-World Mistakes)*

Now you know:

* Why Helm exists
* What Helm is
* Chart structure
* How to write charts
* How to use Helm in CI/CD

Now we talk about something more important:

> 🧠 How to use Helm like a production DevOps engineer (not a beginner)

This chapter is about discipline.

---

# 🎯 1️⃣ Keep Charts Simple (Avoid Over-Engineering)

Helm is for:

* Parameterizing YAML
* Managing environments
* Reusability

Helm is NOT:

* A programming language
* A place for complex logic

❌ Bad Practice:

```yaml
{{ if and (eq .Values.env "prod") (gt .Values.replicaCount 3) }}
```

Too much logic inside templates = hard to maintain.

✅ Good Practice:
Keep logic minimal.
Move environment decisions into values files.

---

# 📂 2️⃣ Values Management Strategy (Very Important)

Structure your project properly:

```
chart/
values.yaml              # Default values
values-dev.yaml
values-staging.yaml
values-prod.yaml
```

### Rules:

* values.yaml → safe defaults
* values-dev.yaml → small resources
* values-prod.yaml → production tuning

Example:

### values-dev.yaml

```yaml
replicaCount: 1
resources:
  requests:
    cpu: "50m"
```

### values-prod.yaml

```yaml
replicaCount: 3
resources:
  requests:
    cpu: "200m"
```

This prevents accidental production misconfiguration.

---

# 🔢 3️⃣ Chart Versioning Strategy

In `Chart.yaml`:

```yaml
version: 1.0.0
appVersion: 2.3.4
```

Understand this clearly:

| Field      | Meaning             |
| ---------- | ------------------- |
| version    | Helm chart version  |
| appVersion | Application version |

If you change:

* Template logic → increase chart version
* Only Docker image → change appVersion

---

# 🚫 4️⃣ Never Use `latest` Image Tag

❌ Bad:

```yaml
image:
  tag: latest
```

Why?

* Unpredictable deployments
* Rollback impossible
* CI/CD chaos

✅ Good:

```yaml
image:
  tag: "1.2.3"
```

Production must always use fixed tags.

---

# 🔐 5️⃣ Don’t Store Secrets in values.yaml

Even if base64 encoded.

❌ Wrong:

```yaml
dbPassword: mypassword123
```

Even in private Git — not safe.

Better options:

* Kubernetes Secret created separately
* External secret manager
* Inject secrets from CI

---

# 🧩 6️⃣ Use helpers.tpl for Reusability

Avoid repeating naming logic everywhere.

Inside `_helpers.tpl`:

```yaml
{{- define "myapp.fullname" -}}
{{ .Release.Name }}-{{ .Chart.Name }}
{{- end }}
```

Then reuse:

```yaml
name: {{ include "myapp.fullname" . }}
```

Benefits:

* Cleaner templates
* Easier refactoring

---

# 🧪 7️⃣ Use helm lint Before Deployment

Always validate:

```bash
helm lint ./chart
```

This catches:

* YAML errors
* Template mistakes
* Invalid syntax

In CI/CD, always add helm lint stage.

---

# 🔍 8️⃣ Use helm template for Debugging

Before deploying:

```bash
helm template myapp ./chart
```

This shows:

* Final Kubernetes YAML
* Rendered output

Very useful for debugging template issues.

---

# 🔄 9️⃣ Keep One Application Per Chart

❌ Bad Practice:
One chart deploys:

* Backend
* Frontend
* Redis
* Database
* Ingress

Too complex.

✅ Good Practice:
One chart = one application.

Dependencies can be separate charts.

---

# 🏷 1️⃣0️⃣ Label Everything Properly

Always include labels:

```yaml
labels:
  app.kubernetes.io/name: {{ .Chart.Name }}
  app.kubernetes.io/instance: {{ .Release.Name }}
```

Why?

* Easier monitoring
* Better filtering
* Production clarity

---

# ⚠️ 1️⃣1️⃣ Common Real-World Mistakes

Let’s talk about mistakes engineers actually make.

---

### ❌ Mistake 1: Too Many `--set` Flags in CI

Example:

```bash
helm upgrade --set a=1 --set b=2 --set c=3 ...
```

Hard to maintain.

Better:
Use environment-specific values files.

---

### ❌ Mistake 2: Mixing Business Logic in Templates

Helm should not decide business logic.
Keep it infrastructure-focused.

---

### ❌ Mistake 3: Huge values.yaml (1000+ lines)

If your values.yaml is massive:

* Break into smaller logical structure
* Or split into sub-charts

---

### ❌ Mistake 4: Not Using Helm History

Helm stores release history:

```bash
helm history myapp
```

Many engineers forget this feature.

---

# 🧠 Production-Ready Helm Checklist

Before deploying to production:

✅ No `latest` tags
✅ Resource requests & limits defined
✅ Probes configured
✅ values files separated by environment
✅ helm lint passed
✅ helm template validated
✅ Secrets handled securely
✅ Chart version updated

If all YES → production-ready.

---

# 🎯 Interview-Level Answer

If asked:

**"What Helm best practices do you follow?"**

You answer:

> "I keep charts simple, separate values per environment, avoid using latest image tags, version charts properly, avoid storing secrets in values files, validate using helm lint, and follow a one-application-per-chart approach."

That is a strong senior-level answer.

---

# 🏁 You Have Completed Helm Section

You now understand:

* Why Helm exists
* Chart structure
* Writing charts
* CI/CD integration
* Production best practices

You are now Helm-proficient at a DevOps level.

---

Next:

📘 **Chapter 42: Kubernetes Interview Questions (Real Answers)**
Or
📘 **Chapter 43: Real-World Kubernetes Scenarios**

Which one should we start with?
