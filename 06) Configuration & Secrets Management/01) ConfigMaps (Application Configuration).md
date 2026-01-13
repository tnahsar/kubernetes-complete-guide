
---

# 📘 Chapter 15: ConfigMaps (Application Configuration)

---

## 1️⃣ Why ConfigMaps Exist

In real applications:

* Config values change between environments

  * dev / test / prod
* Code should **not change** for config changes

❌ Bad practice:

* Hardcoding config in code or image

✅ Kubernetes solution:

> **ConfigMaps externalize configuration from application code**

---

## 2️⃣ Simple Definition

> **A ConfigMap is a Kubernetes object used to store non-sensitive configuration data.**

Examples:

* Environment variables
* Application config files
* Feature flags
* URLs, ports, log levels

---

## 3️⃣ What Should Go Into a ConfigMap?

✔️ Safe, non-sensitive data:

* App name
* API endpoints
* Log level
* Feature flags
* DB hostname (not password)

❌ Do NOT store:

* Passwords
* Tokens
* Private keys

(Those go into **Secrets – Chapter 16**)

---

## 4️⃣ Ways to Use ConfigMaps

ConfigMaps can be consumed in **three main ways**:

1. As **environment variables**
2. As **config files (volume mount)**
3. As **command-line arguments**

We’ll cover the first two (most common).

---

## 5️⃣ ConfigMaps as Environment Variables

### Example ConfigMap

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  APP_ENV: production
  LOG_LEVEL: info
```

### Using It in a Pod

```yaml
envFrom:
  - configMapRef:
      name: app-config
```

Result inside container:

```bash
APP_ENV=production
LOG_LEVEL=info
```

---

## 6️⃣ ConfigMaps as Config Files

Very common for:

* Nginx
* Spring Boot
* Apache
* Custom apps

### Example ConfigMap

```yaml
data:
  app.properties: |
    server.port=8080
    logging.level=INFO
```

### Mounting as Volume

```yaml
volumeMounts:
  - name: config-volume
    mountPath: /etc/config
volumes:
  - name: config-volume
    configMap:
      name: app-config
```

Inside container:

```text
/etc/config/app.properties
```

---

## 7️⃣ ConfigMap Update Behavior (Important)

* Updating a ConfigMap **does NOT automatically restart Pods**
* Mounted files update automatically (with delay)
* Env vars require **Pod restart**

📌 Best practice:

* Restart Pods after ConfigMap changes
* Or use tools like Helm / ArgoCD

---

## 8️⃣ Creating ConfigMaps (Different Ways)

### From YAML

```bash
kubectl apply -f configmap.yaml
```

### From CLI

```bash
kubectl create configmap app-config \
  --from-literal=APP_ENV=prod
```

### From File

```bash
kubectl create configmap app-config \
  --from-file=app.properties
```

---

## 9️⃣ Best Practices for ConfigMaps

✅ Keep config outside images
✅ One ConfigMap per app (or per concern)
✅ Use meaningful keys
✅ Version configs using Git (safe configs only)
❌ Don’t store secrets
❌ Don’t overload one ConfigMap with everything

---

## 🔑 Interview One-Liners

* **ConfigMap** → Stores non-sensitive configuration
* **Separates config from code**
* **Can be consumed as env vars or files**

---

## ⚠️ Common Beginner Mistakes

* ❌ Storing passwords in ConfigMaps
* ❌ Expecting Pods to auto-restart
* ❌ Hardcoding configs inside Docker images

---

## ✅ Key Takeaways

* ConfigMaps manage **application configuration**
* Support env vars and config files
* Help maintain clean, portable applications
* Essential for multi-environment deployments

---