# 📘 Chapter 39: Writing Your Own Helm Chart

*(Converting YAML → Helm, Variables & Templating, Real Example)*

Now you are entering **real DevOps territory**.

Till now:

* You used raw Kubernetes YAML.
* You used existing Helm charts.
* You understand values.yaml and templating.

Now you will learn:

> 🔥 How to convert plain Kubernetes YAML into your own reusable Helm chart.

This is what separates a Kubernetes user from a Kubernetes engineer.

---

# 🎯 1️⃣ Why Write Your Own Helm Chart?

Imagine you have this Deployment YAML:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp
spec:
  replicas: 2
  template:
    spec:
      containers:
      - name: myapp
        image: nginx:1.25.2
        ports:
        - containerPort: 80
```

Problem:

* If environment changes → edit YAML
* If replica changes → edit YAML
* If image changes → edit YAML
* If 5 microservices → 5 similar files

❌ Too much duplication
❌ Hard to maintain
❌ Error-prone

Helm solves this.

---

# 🏗 2️⃣ Step 1: Create a Helm Chart

```bash
helm create myapp-chart
```

It generates:

```
myapp-chart/
  Chart.yaml
  values.yaml
  templates/
  charts/
```

---

# 🧠 3️⃣ Step 2: Convert Static YAML → Template

Let’s convert this:

```yaml
replicas: 2
image: nginx:1.25.2
```

Into dynamic variables.

---

## ✨ Before (Static YAML)

```yaml
replicas: 2
image: nginx:1.25.2
```

---

## 🚀 After (Helm Template)

Inside `templates/deployment.yaml`:

```yaml
spec:
  replicas: {{ .Values.replicaCount }}

containers:
- name: {{ .Chart.Name }}
  image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
```

Now values come from:

---

# 📄 values.yaml

```yaml
replicaCount: 2

image:
  repository: nginx
  tag: "1.25.2"
```

Now:

* Change replicas? → Edit values.yaml
* Change image? → Edit values.yaml
* No template change needed

---

# 🔎 4️⃣ Understanding Templating Syntax

Helm uses **Go templating language**.

### 🔹 {{ .Values.xxx }}

Access values.yaml

### 🔹 {{ .Chart.Name }}

Access Chart metadata

### 🔹 {{ .Release.Name }}

Access release name during install

Example:

```yaml
metadata:
  name: {{ .Release.Name }}
```

Install:

```bash
helm install prod-app myapp-chart
```

Now name becomes:

```
prod-app
```

---

# 🧪 5️⃣ Real Example — Full Deployment Template

Here’s a clean production-style example:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ .Release.Name }}
spec:
  replicas: {{ .Values.replicaCount }}
  selector:
    matchLabels:
      app: {{ .Release.Name }}
  template:
    metadata:
      labels:
        app: {{ .Release.Name }}
    spec:
      containers:
        - name: {{ .Chart.Name }}
          image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
          ports:
            - containerPort: {{ .Values.service.port }}
          resources:
            requests:
              cpu: {{ .Values.resources.requests.cpu }}
              memory: {{ .Values.resources.requests.memory }}
```

---

# 📄 values.yaml (Production-Ready)

```yaml
replicaCount: 2

image:
  repository: nginx
  tag: "1.25.2"

service:
  port: 80

resources:
  requests:
    cpu: "100m"
    memory: "128Mi"
```

Now your chart is:

* Reusable
* Environment configurable
* Clean
* Production-ready

---

# 🔥 6️⃣ Install Your Custom Chart

```bash
helm install myrelease ./myapp-chart
```

Override values:

```bash
helm install myrelease ./myapp-chart \
  --set replicaCount=3
```

Or use custom values file:

```bash
helm install myrelease ./myapp-chart -f prod-values.yaml
```

---

# 🧠 7️⃣ Adding Conditionals (Advanced but Important)

Helm supports condition logic.

Example:

```yaml
{{- if .Values.autoscaling.enabled }}
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
...
{{- end }}
```

values.yaml:

```yaml
autoscaling:
  enabled: true
```

If enabled = false → HPA not created.

This makes charts production flexible.

---

# 🧩 8️⃣ Using helpers.tpl

In `templates/_helpers.tpl`:

```yaml
{{- define "myapp.fullname" -}}
{{ .Release.Name }}-{{ .Chart.Name }}
{{- end }}
```

Use it:

```yaml
name: {{ include "myapp.fullname" . }}
```

This avoids repeating logic everywhere.

---

# ⚠️ 9️⃣ Common Mistakes When Writing Charts

❌ Hardcoding values inside templates
❌ Not using values.yaml
❌ Using latest image tag
❌ No resource limits
❌ No conditional logic
❌ Too much complex logic inside templates

Helm is for configuration, not programming.

---

# 🧠 Mental Model (Very Important)

Think like this:

| Raw YAML        | Helm              |
| --------------- | ----------------- |
| Static          | Dynamic           |
| One environment | Multi-environment |
| Hard to scale   | Reusable          |
| Manual edits    | Values-driven     |

Helm = Parameterized Kubernetes YAML

---

# 🎯 Interview Answer

If asked:

**"How do you create your own Helm chart?"**

You answer:

> "I use helm create to scaffold a chart, then convert static YAML into templates by replacing hardcoded values with .Values variables. I define environment-specific configs in values.yaml and use conditionals when needed. This allows reusable, versioned, and environment-aware deployments."

That’s a strong DevOps-level answer.

---

# 🏁 Where You Stand Now

You now:

* Understand Helm deeply
* Can write your own charts
* Can customize deployments dynamically
* Are production-ready

---

Next chapter:

📘 **Chapter 40: Helm in CI/CD Pipelines**

* GitOps mindset
* Helm in Jenkins
* Helm in GitHub Actions
* Multi-environment strategy

Ready to move to CI/CD integration?
