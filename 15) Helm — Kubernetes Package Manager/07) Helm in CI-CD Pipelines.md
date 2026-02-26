# 📘 Chapter 40: Helm in CI/CD Pipelines

*(GitOps Mindset, Helm in Jenkins / GitHub Actions, Environment Separation)*

Now we move from:

> Writing Helm charts → 🚀 Automating Helm deployments

This is where **real DevOps engineering** begins.

---

# 🎯 1️⃣ Why Helm + CI/CD?

Imagine this manual process:

```bash
helm install myapp ./chart
```

Works fine locally.

But in real companies:

* Developers push code
* Docker image is built
* Image is pushed to registry
* Application is deployed automatically

No manual `helm install`.

That’s CI/CD.

---

# 🧠 2️⃣ The GitOps Mindset (Very Important)

GitOps means:

> Git is the source of truth for deployments.

Instead of manually running commands:

* Code in Git
* Helm chart in Git
* values files in Git
* CI pipeline reads Git and deploys

Production cluster state = Git state

---

# 🏗 3️⃣ Typical CI/CD Flow with Helm

Here’s the real-world flow:

1. Developer pushes code
2. CI pipeline builds Docker image
3. Image pushed to registry (ECR / DockerHub)
4. Helm upgrade runs
5. Kubernetes updates Deployment
6. Rolling update happens

---

# 📊 Visual Flow

```
Git Push
   ↓
CI Pipeline
   ↓
Build Docker Image
   ↓
Push to Registry
   ↓
Helm Upgrade
   ↓
Kubernetes Cluster
```

This is modern DevOps deployment.

---

# 🔧 4️⃣ Helm in Jenkins

Example Jenkins pipeline step:

```groovy
stage('Deploy') {
  steps {
    sh """
    helm upgrade --install myapp ./chart \
      --set image.tag=${BUILD_NUMBER} \
      -f values-prod.yaml
    """
  }
}
```

What happens here?

* `--install` → install if not exists
* `--upgrade` → update existing release
* `image.tag` updated dynamically
* Production values file used

---

# ⚡ 5️⃣ Helm in GitHub Actions

Example:

```yaml
- name: Deploy with Helm
  run: |
    helm upgrade --install myapp ./chart \
      --set image.tag=${{ github.sha }} \
      -f values-prod.yaml
```

Every commit SHA becomes image tag.

Benefits:

* Traceability
* Easy rollback
* Version control

---

# 🌍 6️⃣ Environment Separation Strategy

In real companies:

You don’t deploy same config everywhere.

Structure:

```
chart/
values-dev.yaml
values-staging.yaml
values-prod.yaml
```

Deploy per environment:

### Dev

```bash
helm upgrade --install myapp ./chart -f values-dev.yaml
```

### Staging

```bash
helm upgrade --install myapp ./chart -f values-staging.yaml
```

### Production

```bash
helm upgrade --install myapp ./chart -f values-prod.yaml
```

Each file can define:

* Replicas
* Resources
* HPA settings
* Image tags
* External URLs

This is clean environment management.

---

# 🔁 7️⃣ Helm Rollback in CI/CD

If deployment fails:

```bash
helm rollback myapp 1
```

Helm stores release history.

CI pipelines can automate rollback on failure.

---

# 🔐 8️⃣ Secrets Handling in CI/CD

Never store secrets inside values.yaml in Git.

Better approaches:

* Use external secret manager
* Inject secrets during pipeline
* Use Kubernetes Secret objects separately

Example:

```bash
helm upgrade myapp ./chart \
  --set image.tag=1.2.3 \
  --set secrets.dbPassword=$DB_PASSWORD
```

Environment variable passed securely from CI.

---

# 🚀 9️⃣ Advanced: GitOps Tools with Helm

Instead of running Helm directly from Jenkins, many companies use:

* ArgoCD
* Flux

Flow becomes:

Git → ArgoCD watches → Auto sync → Deploy

Helm chart is stored in Git, and ArgoCD applies it automatically.

This is full GitOps model.

---

# 📦 1️⃣0️⃣ Versioning Strategy

Best practice:

* Chart version → in Chart.yaml
* App version → in values.yaml
* Docker image version → tag

Example:

```yaml
version: 1.2.0
appVersion: 2.3.1
```

Clear separation between:

* Chart version
* Application version

---

# 🧠 Real Production Deployment Example

Let’s say:

* Repo: backend-service
* Chart inside repo
* Jenkins pipeline:

  * Build image
  * Push to ECR
  * Helm upgrade with new tag
  * Monitor rollout

Zero manual intervention.

---

# 🎯 Interview-Level Answer

If asked:

**"How do you use Helm in CI/CD?"**

You answer:

> "In our CI pipeline, we build and push the Docker image, then use helm upgrade --install to deploy to the cluster. We pass dynamic image tags using --set and use environment-specific values files. For production, we follow GitOps principles and maintain all configuration in Git."

That’s a strong production-level answer.

---

# 🧠 Mental Model Summary

| Without CI/CD         | With Helm + CI/CD      |
| --------------------- | ---------------------- |
| Manual deployment     | Automated              |
| Human errors          | Consistent             |
| Hard rollback         | helm rollback          |
| Environment confusion | values per environment |

Helm + CI/CD = Controlled, repeatable deployments.

---

# 🏁 You Have Now Completed

You now understand:

* Writing Helm charts
* Templating
* values.yaml
* CI/CD integration
* GitOps concept
* Environment strategy

This is production-ready Kubernetes.

---

Next:

📘 **Chapter 41: Helm Best Practices**

* Chart structure discipline
* Values management strategy
* Common real-world mistakes

Ready to go deeper into production discipline?
