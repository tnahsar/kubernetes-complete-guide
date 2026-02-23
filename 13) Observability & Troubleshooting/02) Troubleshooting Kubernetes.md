# 📘 Chapter 31: Troubleshooting Kubernetes

Now we move into one of the **most important real-world skills**:

> Debugging Kubernetes 🔍

In interviews and production, this matters more than theory.

Pods will fail.
Containers will crash.
Services won’t connect.

Your job is to **stay calm and debug step by step**.

---

# 🧠 Golden Rule of Troubleshooting

Never guess.

Always check in this order:

1. Pod status
2. Events
3. Logs
4. Describe output
5. Resource usage

Kubernetes already gives you everything — you just need to know where to look.

---

# 🧱 1️⃣ Understanding Pod States

Run:

```bash
kubectl get pods
```

You might see:

* Running ✅
* Pending ⏳
* CrashLoopBackOff 🔁
* ImagePullBackOff 📦
* Error ❌
* Completed ✔

Let’s understand the important ones.

---

## 🔴 Pending

Meaning:

> Pod is not scheduled yet.

Possible reasons:

* No available nodes
* Insufficient CPU/memory
* Taints blocking it
* NodeSelector mismatch

Check:

```bash
kubectl describe pod pod-name
```

Look at:

```
Events:
```

Events usually tell the exact reason.

---

## 🔁 CrashLoopBackOff

Meaning:

> Container starts → crashes → restarts → crashes again.

Common reasons:

* Application error
* Wrong environment variables
* Database not reachable
* Port already in use

Check logs:

```bash
kubectl logs pod-name
```

---

## 📦 ImagePullBackOff

Meaning:

> Kubernetes cannot pull container image.

Possible reasons:

* Wrong image name
* Wrong tag
* Private registry without secret
* No internet access

Check image name in YAML carefully.

---

# 🛠 2️⃣ The Most Important Command: describe

```bash
kubectl describe pod pod-name
```

This shows:

* Node where Pod scheduled
* Container status
* Events
* Restart count
* Resource issues

Always check Events section.

It tells:

* Failed scheduling
* Failed mount
* Insufficient memory
* Taint issues

---

# 📄 3️⃣ Logs (Application-Level Debugging)

If Pod is Running but app not working:

```bash
kubectl logs pod-name
```

For previous crash:

```bash
kubectl logs pod-name --previous
```

This is very useful for CrashLoopBackOff.

---

# 📊 4️⃣ Check Resource Problems

If Pod keeps restarting:

Maybe memory exceeded limit.

Check:

```bash
kubectl top pods
```

If memory usage > limit → OOMKilled.

In `describe`, look for:

```
Reason: OOMKilled
```

That means:

* Increase memory limit
* Fix memory leak

---

# 🌐 5️⃣ Service Not Working?

If app is running but cannot connect:

Check:

1. Is Service created?

```bash
kubectl get svc
```

2. Does Service selector match Pod labels?

Mismatch is very common mistake.

Example:

Service selector:

```yaml
app: backend
```

Pod label:

```yaml
app: api
```

They must match exactly.

---

# 🛣 6️⃣ Ingress Not Working?

Check:

* Is Ingress Controller installed?
* Is DNS pointing correctly?
* Does Service work internally?

Test internally:

```bash
kubectl port-forward service/backend 8080:80
```

If it works locally → Ingress problem.

---

# 🔐 7️⃣ Secret or ConfigMap Issues

Common error:

Application crashes because:

* Missing environment variable
* Wrong Secret key name
* ConfigMap not mounted

Check:

```bash
kubectl describe pod pod-name
```

Look for:

```
MountVolume.SetUp failed
```

---

# 🧠 Real Troubleshooting Workflow (Step-by-Step)

Imagine:

User says:

> Website not loading.

You should:

1️⃣ Check Pods

```
kubectl get pods
```

2️⃣ Check Service

```
kubectl get svc
```

3️⃣ Check Ingress

```
kubectl get ingress
```

4️⃣ Describe failing object

```
kubectl describe pod
```

5️⃣ Check logs

```
kubectl logs
```

6️⃣ Check resources

```
kubectl top pods
```

Never jump randomly.

---

# 🚨 Common Failure Patterns

| Problem            | Likely Cause          |
| ------------------ | --------------------- |
| Pod Pending        | No resources          |
| CrashLoopBackOff   | App crash             |
| OOMKilled          | Memory limit too low  |
| 502 Bad Gateway    | Service/Ingress issue |
| Connection refused | Wrong port            |
| ImagePullBackOff   | Wrong image           |

---

# 🏆 Interview-Level Troubleshooting Answer

If interviewer asks:

> How do you troubleshoot a failing Pod?

Good answer:

1. Check Pod status
2. Describe Pod
3. Check Events
4. Check Logs
5. Check resource usage
6. Verify Service & networking

Structured answer impresses.

---

# 🧠 Advanced Tip: Debug Inside Container

You can exec into Pod:

```bash
kubectl exec -it pod-name -- /bin/sh
```

Then:

* Check files
* Check env variables
* Test curl internally

Very powerful.

---

# 🏗 Production Mindset

Good engineers:

* Add probes
* Set resource limits
* Configure logging
* Set alerts
* Use dashboards

So failures are detected early.

---

# 📌 Chapter 31 Summary

Troubleshooting means:

* Read Pod status
* Check Events
* Inspect logs
* Verify Services
* Check resources
* Debug step-by-step

Kubernetes gives you tools.

You just need the method.

---

Next we move to:

# 📘 Chapter 32: Production Best Practices

(Where we learn how to run Kubernetes properly in real companies 🏢)

Ready to continue?
