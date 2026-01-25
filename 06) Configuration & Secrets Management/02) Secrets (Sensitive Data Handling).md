
---

# 📘 Chapter 16: Secrets (Sensitive Data Handling)

This chapter explains **how Kubernetes handles sensitive information** like passwords, tokens, and keys — **safely and correctly**.

Think of this chapter as **“ConfigMaps for secrets, but with extra care.”**

---

## 🔹 Why Secrets Are Needed (The Problem)

Applications need sensitive data such as:

* Database passwords
* API tokens
* TLS certificates
* Cloud credentials

❌ **Hard-coding secrets is dangerous**

* Anyone with repo access can see them
* Secrets leak in logs, screenshots, or CI pipelines
* Changing passwords requires rebuilding images

👉 **Kubernetes Secrets exist to separate sensitive data from application code.**

---

## 🔹 What Is a Kubernetes Secret?

A **Secret** is a Kubernetes object used to store **small amounts of sensitive data**.

📌 Examples:

* `DB_PASSWORD`
* `AWS_ACCESS_KEY`
* `JWT_SECRET`
* TLS certificates

✅ Secrets can be:

* Injected as **environment variables**
* Mounted as **files in a pod**

---

## 🔹 Secret vs ConfigMap (Important Difference)

| ConfigMap                  | Secret            |
| -------------------------- | ----------------- |
| Non-sensitive data         | Sensitive data    |
| Plain text                 | Base64 encoded    |
| Safe to commit (sometimes) | ❌ Never commit    |
| App configs                | Passwords, tokens |

⚠️ **Secrets are NOT encrypted by default** — this is a very common misconception.

---

## 🔹 Types of Secrets in Kubernetes

### 1️⃣ Generic Secrets

Used for:

* Passwords
* Tokens
* Keys

Example:

* Database credentials

---

### 2️⃣ TLS Secrets

Used for:

* HTTPS certificates
* Ingress TLS

Stores:

* `tls.crt`
* `tls.key`

---

### 3️⃣ Docker Registry Secrets

Used for:

* Pulling private images

Stores:

* Docker registry username/password

---

## 🔹 How Secrets Are Used by Applications

### 1️⃣ As Environment Variables

* Secret key → environment variable
* App reads it at runtime

Example:

```
DB_PASSWORD=********
```

📌 **Most common usage**

---

### 2️⃣ As Files (Mounted Volumes)

* Each secret key becomes a file
* App reads file content

Example:

```
/etc/secrets/db_password
```

📌 Preferred for certificates & keys

---

## 🔹 Base64 vs Encryption (Very Important)

### ❓ Why Base64?

Kubernetes stores secrets in **Base64** format because:

* YAML supports text only
* Binary data must be encoded

⚠️ **Base64 is NOT encryption**

* Anyone can decode it easily

Example:

```
echo cGFzc3dvcmQ= | base64 -d
```

---

### 🔐 How to Make Secrets More Secure

To truly secure secrets in production:

* Enable **etcd encryption at rest**
* Use **external secret managers**:

  * AWS Secrets Manager
  * HashiCorp Vault
  * Azure Key Vault
* Use **RBAC** to limit secret access

---

## 🔹 What NOT to Store in Git (Critical)

❌ Never store:

* Plain secrets
* Base64 secrets
* values.yaml with passwords
* `.env` files with real credentials

Even private repos are **not safe**.

---

## 🔹 Best Practices (Interview Gold ⭐)

✅ Use Secrets, not ConfigMaps
✅ Restrict access using RBAC
✅ Mount secrets only where needed
✅ Rotate secrets regularly
✅ Use external secret managers in prod
✅ Never log secret values

---

## 🔹 Real-World Example (Mental Model)

> “ConfigMap is for **how your app behaves**
> Secret is for **what your app must protect**”

---

## 🔹 Interview-Ready Summary

> Kubernetes Secrets store sensitive data like passwords and tokens.
> They are Base64-encoded (not encrypted) and should be accessed via env vars or mounted files.
> For production, etcd encryption and external secret managers should be used.

---