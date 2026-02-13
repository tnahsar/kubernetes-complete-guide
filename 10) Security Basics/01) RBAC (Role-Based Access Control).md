
---

# 📘 Chapter 23: RBAC (Role-Based Access Control)

---

## 🔹 1️⃣ Why RBAC Is Needed

In Kubernetes:

* Many **users, teams, and applications** access the cluster
* Not everyone should have **admin access**
* Without access control:

  * Anyone could delete Pods
  * Anyone could change configs
  * High risk in production

👉 **RBAC controls WHO can do WHAT on WHICH resources**

---

## 🔹 2️⃣ What Is RBAC?

> **RBAC = Role-Based Access Control**

Kubernetes checks **every API request** and answers:

1. **Who** is making the request?
2. **What** action are they trying to do?
3. **On which resource**?
4. **Is it allowed?**

If allowed → request succeeds
If not → request is denied ❌

---

## 🔹 3️⃣ Core RBAC Building Blocks

RBAC has **4 main objects**:

| Object             | Purpose                                 |
| ------------------ | --------------------------------------- |
| Role               | Defines permissions inside a namespace  |
| ClusterRole        | Defines permissions cluster-wide        |
| RoleBinding        | Attaches Role to a user/service account |
| ClusterRoleBinding | Attaches ClusterRole cluster-wide       |

---

## 🔹 4️⃣ Role (Namespace-Level Permissions)

A **Role** defines:

* Allowed **verbs** (get, list, create, delete, etc.)
* Allowed **resources** (pods, services, configmaps, etc.)
* Inside **one namespace**

### Example Role

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: pod-reader
  namespace: dev
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "list"]
```

This role allows:

* Read Pods (`get`, `list`)
* Only inside `dev` namespace

---

## 🔹 5️⃣ ClusterRole (Cluster-Wide Permissions)

A **ClusterRole**:

* Works across **all namespaces**
* Can control **cluster-level resources**
* Can also be reused in multiple namespaces

### Example ClusterRole

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: pod-admin
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["*"]
```

This allows **full Pod access everywhere**.

---

## 🔹 6️⃣ RoleBinding (Attach Role to User)

A **RoleBinding** connects:

* Role → User / Group / ServiceAccount
* Inside **one namespace**

### Example RoleBinding

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: read-pods
  namespace: dev
subjects:
- kind: User
  name: dev-user
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```

👉 `dev-user` can **read Pods in dev namespace only**

---

## 🔹 7️⃣ ClusterRoleBinding (Cluster-Level Attach)

A **ClusterRoleBinding**:

* Attaches ClusterRole
* Applies to **entire cluster**

### Example

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-binding
subjects:
- kind: User
  name: admin-user
roleRef:
  kind: ClusterRole
  name: cluster-admin
  apiGroup: rbac.authorization.k8s.io
```

👉 `admin-user` gets **full cluster access**

---

## 🔹 8️⃣ Verbs & Resources (Very Important)

Common **verbs**:

* get, list, watch
* create, update, patch
* delete

Common **resources**:

* pods
* deployments
* services
* configmaps
* secrets

> RBAC rules are **explicit** → no implicit permissions

---

## 🔹 9️⃣ Least Privilege Principle (Must Know)

> Give **minimum permissions required** — nothing more.

❌ Bad:

* Give `cluster-admin` to everyone

✅ Good:

* Read-only access where possible
* Namespace-scoped roles
* Separate roles for CI/CD, developers, admins

---

## 🔹 🔟 Mental Model (Easy Analogy)

Think of Kubernetes like an **office building**:

* **Role** → job description
* **RoleBinding** → assigning job to employee
* **ClusterRole** → company-wide job
* **Least privilege** → employee gets only required access

---

## 🔹 1️⃣1️⃣ Key Takeaways (Interview-Friendly)

* RBAC controls **access to Kubernetes API**
* Role → namespace-level
* ClusterRole → cluster-level
* RoleBinding / ClusterRoleBinding attach permissions
* Always follow **least privilege**

---