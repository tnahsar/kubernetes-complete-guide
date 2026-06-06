Awesome 👍
Let’s continue with **Chapter 24: Service Accounts** — this is where **RBAC meets real workloads**.

---

# 📘 Chapter 24: Service Accounts

---

## 🔹 1️⃣ Why Service Accounts Are Needed

So far we talked about **users** accessing Kubernetes (via kubectl).

But in real clusters:

* Pods run **applications**
* Applications also need to talk to Kubernetes API

  * Read ConfigMaps
  * Watch Pods
  * Create Jobs
* Apps should **NOT use human credentials**

👉 That’s why **Service Accounts exist**.

---

## 🔹 2️⃣ What Is a Service Account?

> A **Service Account** is an identity for **applications (Pods)**, not humans.

* Used by Pods to authenticate to Kubernetes API
* Works with **RBAC**
* Namespace-scoped

---

## 🔹 3️⃣ Default Service Account

Every namespace has a default ServiceAccount:

```bash
kubectl get sa
```

Output:

```text
NAME      SECRETS   AGE
default   1         10d
```

* Pods use this **by default**
* Has **very limited permissions**
* Not suitable for most real use cases

---

## 🔹 4️⃣ How Service Accounts Work (Behind the Scenes)

When a Pod uses a ServiceAccount:

1. Kubernetes creates a **token**
2. Token is mounted into the Pod as a file
3. App uses this token to call Kubernetes API
4. API Server checks:

   * ServiceAccount identity
   * RBAC permissions

📁 Token location inside Pod:

```
/var/run/secrets/kubernetes.io/serviceaccount/
```

---

## 🔹 5️⃣ Creating a Service Account

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: app-sa
  namespace: dev
```

This creates a ServiceAccount named `app-sa`.

---

## 🔹 6️⃣ Assigning Service Account to a Pod

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-app
spec:
  serviceAccountName: app-sa
  containers:
  - name: app
    image: nginx
```

👉 Now this Pod uses `app-sa` identity.

---

## 🔹 7️⃣ Service Account + RBAC (Very Important)

ServiceAccounts **have no power by default**.

You must:

1. Create Role / ClusterRole
2. Bind it using RoleBinding / ClusterRoleBinding

### Example: Allow Pod to read ConfigMaps

**Role**

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: cm-reader
  namespace: dev
rules:
- apiGroups: [""]
  resources: ["configmaps"]
  verbs: ["get", "list"]
```

**RoleBinding**

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: cm-reader-binding
  namespace: dev
subjects:
- kind: ServiceAccount
  name: app-sa
roleRef:
  kind: Role
  name: cm-reader
  apiGroup: rbac.authorization.k8s.io
```

👉 Now Pods using `app-sa` can read ConfigMaps.

---

## 🔹 8️⃣ Real-World Use Cases

Common scenarios:

* CI/CD Pods deploying resources
* Monitoring agents reading cluster metrics
* Controllers (Ingress, Operators)
* Jobs creating other Jobs or Pods

---

## 🔹 9️⃣ Security Best Practices

✅ Create **dedicated ServiceAccounts**
✅ Never use `default` in production
✅ Follow **least privilege**
✅ Rotate tokens when needed
✅ Disable API access if not required:

```yaml
automountServiceAccountToken: false
```

---

## 🔹 🔟 Mental Model (Easy Analogy)

Think of Service Accounts as **robot employees**:

* Humans → users
* Robots → ServiceAccounts
* RBAC → what each robot is allowed to do

---

## 🔹 1️⃣1️⃣ Key Takeaways (Interview-Friendly)

* ServiceAccount = identity for Pods
* Used by apps to talk to Kubernetes API
* Works with RBAC
* Default ServiceAccount is **unsafe for prod**
* Always assign minimal permissions

---

### ✅ Chapter 24 Completed 🎉

---

### 👉 Next Chapter

Next we move to **PART 10: Advanced Workloads**

## **Chapter 25: StatefulSet (Stateful Applications)**

* Databases in Kubernetes
* Stable identities
* Ordered deployments

Just say:
**“Continue with Chapter 25”** 📘🚀
