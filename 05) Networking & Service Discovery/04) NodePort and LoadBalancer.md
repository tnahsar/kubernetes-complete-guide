
---

## 🚫 When **NOT** to use `NodePort`

### 1️⃣ **Production / public-facing apps**

**Why it’s bad:**

* Exposes a **high, ugly port** (`30080`)
* No TLS termination
* No smart routing (paths, hosts, rewrites)

---

## ✅ What to use instead (most of the time)

### 🥇 **Ingress (BEST for HTTP/HTTPS)**

**Use when:**

* Web apps
* APIs
* Multiple services
* Need TLS

**Why it’s great:**

* Single entry point (80/443)
* Host-based & path-based routing
* TLS termination
* Clean URLs

```text
https://api.myapp.com
https://myapp.com/login
```

👉 Production standard.

---

### 🥈 **LoadBalancer Service**

**Use when:**

* Cloud environment (AWS, GCP, Azure)
* Simple external exposure
* Non-HTTP traffic

**What happens:**

```yaml
type: LoadBalancer
```

➡ Cloud creates a real external load balancer
➡ Public IP assigned automatically

---

## 🧠 The mental model (remember this)

| Goal          | Use          |
| ------------- | ------------ |
| Learn / test  | NodePort     |
| Real users    | Ingress      |
| Cloud infra   | LoadBalancer |
| Internal only | ClusterIP    |

---

# 5️⃣ NodePort Is Hard for Large Production Systems

Imagine:

```text id="l1m’wini"
frontend-service → 30080
api-service → 30081
admin-service → 30082
```

Managing many ports becomes messy.

---

# LoadBalancer + Ingress Solves This

```text id="x9m’wini"
myapp.com
api.myapp.com
admin.myapp.com
```

all using:
* standard ports 80/443
---