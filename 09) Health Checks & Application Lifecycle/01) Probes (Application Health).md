
---

# 📘 Chapter 21: Probes (Application Health)

---

## 🔹 1️⃣ Why Probes Are Needed

In Kubernetes, Pods may:

* Crash unexpectedly
* Hang due to deadlocks
* Start slowly and not be ready immediately

Without health checks:

* Traffic might be sent to **unhealthy Pods**
* Users experience downtime
* Manual intervention is required

**Probes automate health monitoring** and help Kubernetes **self-heal**.

---

## 🔹 2️⃣ Types of Probes

Kubernetes provides **3 main probes**:

| Probe Type      | Purpose                                                 |
| --------------- | ------------------------------------------------------- |
| Liveness Probe  | Is the app alive? Restart if it’s dead                  |
| Readiness Probe | Is the app ready to serve traffic?                      |
| Startup Probe   | Has the app finished starting? Avoid premature restarts |

---

## 🔹 3️⃣ Liveness Probe

* Detects **dead or stuck containers**
* If the probe fails → **kubelet kills the container**, Pod restarts

### Example: HTTP Liveness Probe

```yaml
livenessProbe:
  httpGet:
    path: /healthz
    port: 8080
  initialDelaySeconds: 10
  periodSeconds: 5
```

* `initialDelaySeconds` → wait before first check
* `periodSeconds` → check interval
* Kubernetes restarts Pod if `/healthz` returns failure

---

## 🔹 4️⃣ Readiness Probe

* Checks if the app is **ready to accept traffic**
* Failed readiness → Pod **removed from Service endpoints**
* Useful for **slow-starting apps or maintenance windows**

### Example: TCP Readiness Probe

```yaml
readinessProbe:
  tcpSocket:
    port: 3306
  initialDelaySeconds: 5
  periodSeconds: 10
```

* Pod exists but **traffic is not sent** until ready

---

## 🔹 5️⃣ Startup Probe

* For **very slow-starting applications**
* Prevents Kubernetes from **restarting the Pod before it’s ready**
* Only used once, then liveness probe takes over

### Example:

```yaml
startupProbe:
  httpGet:
    path: /startup
    port: 8080
  failureThreshold: 30
  periodSeconds: 10
```

* Gives app **enough time to start** without being killed

---

## 🔹 6️⃣ Probe Options

| Field               | Meaning                                        |
| ------------------- | ---------------------------------------------- |
| initialDelaySeconds | Delay before first probe                       |
| periodSeconds       | Frequency of probe                             |
| timeoutSeconds      | Time to wait for probe response                |
| successThreshold    | Number of successes before considered healthy  |
| failureThreshold    | Number of failures before considered unhealthy |

---

## 🔹 7️⃣ Mental Model (Easy Analogy)

Think of Pods as **restaurants**:

* **Liveness Probe** → Is the restaurant open or burned down?
* **Readiness Probe** → Is the kitchen ready to serve customers?
* **Startup Probe** → Is the restaurant still under setup/construction?

Kubernetes then **directs customers (traffic) only to ready restaurants**.

---

## 🔹 8️⃣ Best Practices

✅ Use **readiness probes** for slow-starting apps
✅ Use **liveness probes** for crash detection
✅ Avoid aggressive settings → prevent flapping (Pod restarts too often)
✅ Combine with **resource limits** for better stability

---

## 🔹 9️⃣ Key Takeaways (Interview-Friendly)

* Liveness → Is app alive?
* Readiness → Is app ready for traffic?
* Startup → Has app fully started?
* Probes prevent **traffic to unhealthy Pods**
* Helps Kubernetes **self-heal** and maintain **high availability**

---
