# 📘 Chapter 37: Helm Chart Structure (Line-by-Line)

Now we open the Helm chart and see:

> What is inside it?

This chapter is very important because:

If you understand chart structure,
you can write your own charts confidently.

---

# 🧠 First: Create a Sample Chart

Run:

```bash
helm create myapp
```

Helm generates a folder:

```
myapp/
```

Inside, you will see something like:

```
myapp/
  Chart.yaml
  values.yaml
  charts/
  templates/
  .helmignore
```

Now let’s understand each file clearly.

---

# 📄 1️⃣ Chart.yaml

This file contains:

> Metadata about the chart

Example:

```yaml
apiVersion: v2
name: myapp
description: A Helm chart for Kubernetes
type: application
version: 0.1.0
appVersion: "1.0"
```

---

## 🧠 What Each Field Means

* apiVersion → Chart format version
* name → Chart name
* description → Short info
* type → application or library
* version → Chart version (important)
* appVersion → Application version (Docker image version)

Important:

Chart version ≠ App version

---

# 📄 2️⃣ values.yaml

This is the heart of configuration.

It contains:

> Default values used by templates.

Example:

```yaml
replicaCount: 1

image:
  repository: nginx
  tag: latest

service:
  type: ClusterIP
  port: 80
```

You can override values during install:

```bash
helm install myapp ./myapp -f values-prod.yaml
```

Or directly:

```bash
helm install myapp ./myapp --set replicaCount=3
```

---

# 📁 3️⃣ templates/ Folder

This is the most important folder.

It contains Kubernetes YAML templates like:

```
templates/
  deployment.yaml
  service.yaml
  ingress.yaml
  _helpers.tpl
```

These are not normal YAML files.

They contain:

> Go template syntax.

Example:

```yaml
replicas: {{ .Values.replicaCount }}
```

Helm replaces this with actual value.

---

# 🔧 Example: deployment.yaml Template

Inside template:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ .Release.Name }}
spec:
  replicas: {{ .Values.replicaCount }}
```

Helm replaces:

* .Release.Name → release name
* .Values.replicaCount → value from values.yaml

Then sends final YAML to Kubernetes.

---

# 🧩 4️⃣ _helpers.tpl

This file contains:

> Reusable template functions

Example:

```yaml
{{- define "myapp.fullname" -}}
{{ .Release.Name }}-{{ .Chart.Name }}
{{- end }}
```

Then used inside templates:

```yaml
name: {{ include "myapp.fullname" . }}
```

This avoids repetition.

Think of it like:

* Helper functions in programming.

---

# 📁 5️⃣ charts/ Folder

Used for:

> Chart dependencies

If your chart depends on another chart (like Redis),
it will be placed inside this folder.

Example:

```
charts/
  redis-17.3.0.tgz
```

Helm can automatically install dependencies.

---

# 📄 6️⃣ .helmignore

Works like:

.gitignore

Specifies which files to ignore when packaging chart.

---

# 🧠 Complete Structure Recap

```
myapp/
  Chart.yaml        → Chart metadata
  values.yaml       → Default configuration
  templates/        → Kubernetes templates
    deployment.yaml
    service.yaml
    ingress.yaml
    _helpers.tpl
  charts/           → Dependencies
  .helmignore       → Ignore rules
```

This is the standard Helm chart structure.

---

# 🧪 What Happens During Install?

When you run:

```bash
helm install myapp ./myapp
```

Helm:

1. Reads Chart.yaml
2. Reads values.yaml
3. Reads templates/
4. Replaces template variables
5. Generates final YAML
6. Sends to Kubernetes API

---

# 🎯 Important Template Variables

Here are commonly used ones:

| Variable       | Meaning                  |
| -------------- | ------------------------ |
| .Values        | Access values.yaml       |
| .Release.Name  | Release name             |
| .Chart.Name    | Chart name               |
| .Chart.Version | Chart version            |
| .Namespace     | Namespace                |
| include        | Include helper templates |

---

# 🏗 Real Production Usage

In companies:

* Each microservice has its own chart
* values-dev.yaml
* values-staging.yaml
* values-prod.yaml
* Chart version maintained properly
* Templates standardized

Helm structure keeps deployments organized.

---

# 🚨 Common Beginner Mistakes

❌ Editing rendered YAML instead of templates
❌ Hardcoding values inside templates
❌ Not using helpers
❌ Confusing appVersion with chart version
❌ Overusing --set in production

---

# 🏆 Interview-Level Insight

If interviewer asks:

> What is inside a Helm chart?

Strong answer:

* Chart.yaml (metadata)
* values.yaml (default configuration)
* templates/ (Kubernetes manifests with Go templating)
* helpers.tpl (reusable template functions)
* charts/ (dependencies)

---

# 📌 Chapter 37 Summary

A Helm chart contains:

* Metadata
* Configuration values
* Template files
* Helper templates
* Optional dependencies

Helm chart = Kubernetes app package with templating.

---

Next:

# 📘 Chapter 38: Using Helm Charts

(We’ll learn install, upgrade, rollback, uninstall with real commands.)

Ready to continue?
