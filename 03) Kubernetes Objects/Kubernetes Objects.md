# 📘 Kubernetes Objects

## 🧠 What Is a Kubernetes Object?

A Kubernetes object is a resource inside the Kubernetes cluster that defines the desired state of an application or infrastructure component.

When you create an object using a YAML manifest or Kubernetes API, Kubernetes continuously works to ensure that the actual state matches the desired state defined in the object.

This reconciliation process is one of the core principles of Kubernetes orchestration.

---

## ⚙️ Basic Structure of a Kubernetes Object

Every Kubernetes object is defined using fields such as:

```yaml
apiVersion: <api-version>
kind: <object-type>

metadata:
  name: <object-name>
```

Example:

```yaml
apiVersion: v1
kind: Pod

metadata:
  name: nginx-pod
```

### 🧠 Important Fields

| Field        | Purpose                                                     |
| ------------ | ----------------------------------------------------------- |
| `apiVersion` | Defines which Kubernetes API version handles the object     |
| `kind`       | Defines the object type                                     |
| `metadata`   | Stores object information such as name, labels, annotations |
| `spec`       | Defines the desired state                                   |
| `status`     | Stores the current actual state (managed by Kubernetes)     |

---

## 🏗️ Major Categories of Kubernetes Objects

Kubernetes objects can be grouped based on the `orchestration capability` they provide.

## 1️⃣ Workload Objects

Workload objects run and manage application containers.

These are among the most important Kubernetes objects.

---

### 📦 Pod

The smallest deployable unit in Kubernetes.

A Pod may contain:

* one container
* or multiple tightly coupled containers

Pods provide:

* Shared network namespace
* Shared storage volumes
* Shared lifecycle
* Inter-container communication using localhost

Pods are ephemeral by nature.

---

### 🔁 ReplicaSet

ReplicaSet manages Pods.

Provides:

* Self-healing
* Replica management
* High availability

If a Pod crashes:

```text
ReplicaSet automatically recreates it
```

---

### 🚀 Deployment

Deployment manages ReplicaSets.

Provides:

* Rolling updates
* Rollbacks
* Declarative application upgrades
* Scaling

Typical hierarchy:

```text
Deployment
    ↓
ReplicaSet
    ↓
Pods
    ↓
Containers
```

Deployment is one of the most commonly used Kubernetes objects in production.

---

### 🗄️ StatefulSet

Used for stateful applications.

Provides:

* Stable Pod names
* Stable network identity
* Persistent storage association
* Ordered startup and shutdown

Commonly used for:

* MySQL
* PostgreSQL
* MongoDB
* Kafka

---

### 🖥️ DaemonSet

Ensures one Pod runs on every node.

Common use cases:

* Log collection
* Monitoring agents
* Security agents
* Node exporters

Example:

```text
1 Pod per worker node
```

---

### ✅ Job

Runs tasks until completion.

Used for:

* Database migration
* Batch processing
* Data import/export
* One-time scripts

Jobs are not designed for continuously running applications.

---

### ⏰ CronJob

Runs Jobs on a schedule.

Examples:

* Daily backup
* Weekly cleanup
* Scheduled report generation

Similar to Linux cron scheduling.

---

## 2️⃣ Networking Objects

Networking objects enable communication between applications.

### 🌐 Service

Provides:

* Stable virtual IP
* Service discovery
* Internal load balancing
* Network abstraction for Pods

Services route traffic to Pods using label selectors.

Without Services:

* Pod IP changes would break communication

---

### 🌍 Ingress

Provides centralized HTTP/HTTPS access into the cluster.

Supports:

* Path-based routing
* Host-based routing
* TLS termination
* Reverse proxy functionality

Typical flow:

```text
Internet
    ↓
Ingress
    ↓
Service
    ↓
Pods
```

Ingress is commonly used in microservices architectures.

---

### 🔐 NetworkPolicy

Controls Pod-to-Pod network communication.

Provides:

* Traffic filtering
* Namespace isolation
* Micro-segmentation
* Network-level security

Without NetworkPolicy:

```text
All Pods can usually communicate with each other
```

(depending on CNI plugin behavior)

---

## 3️⃣ Storage Objects

Storage objects manage persistent application data.

### 💾 PersistentVolume (PV)

Represents actual storage available to the cluster.

Examples:

* AWS EBS
* Azure Disk
* GCP Persistent Disk
* NFS
* Ceph

PV is usually created dynamically by Kubernetes or storage providers.

---

### 📦 PersistentVolumeClaim (PVC)

Requests storage from Kubernetes.

Applications generally consume PVCs rather than directly using PVs.

Example request:

```text
Need 10Gi SSD storage
```

---

### ⚡ StorageClass

Defines how storage should be provisioned.

Examples:

* fast-ssd
* standard-hdd

Enables:

* dynamic storage provisioning
* storage tier management

---

## 4️⃣ Configuration Objects

Used to externalize application configuration.

---

### ⚙️ ConfigMap

Stores non-sensitive configuration data.

Examples:

* Environment variables
* Application settings
* Config files
* Feature flags

Helps separate:

* application code
* configuration

---

### 🔑 Secret

Stores sensitive data.

Examples:

* Passwords
* API keys
* Tokens
* Certificates

Secrets are Base64-encoded by default and should ideally be encrypted at rest.

---

## 5️⃣ Security & Access Control Objects

Used for authentication and authorization.

### 👤 ServiceAccount

Provides an identity for Pods.

Used when Pods need:

* Kubernetes API access
* cloud IAM integration
* RBAC permissions

---

### 🔐 Role

Defines permissions inside a namespace.

Example:

```text
Read Pods inside development namespace
```

---

### 🔗 RoleBinding

Assigns a Role to:

* users
* groups
* ServiceAccounts

---

### 🌐 ClusterRole

Defines cluster-wide permissions.

Examples:

* Node access
* Namespace management
* Cluster-level monitoring

---

### 🌐 ClusterRoleBinding

Assigns ClusterRoles to:

* users
* groups
* ServiceAccounts

---

## 6️⃣ Namespace Objects

### 🏷️ Namespace

Provides logical isolation inside a cluster.

Common examples:

```text
production
staging
development
monitoring
```

Namespaces help with:

* Resource organization
* Access control
* Multi-team isolation
* Resource quotas

---

# 📊 Summary Table

| Object                  | Primary Capability                   |
| ----------------------- | ------------------------------------ |
| `Pod`                   | Smallest deployable unit             |
| `ReplicaSet`            | Self-healing and scaling             |
| `Deployment`            | Rolling updates and rollbacks        |
| `StatefulSet`           | Stateful workloads                   |
| `DaemonSet`             | One Pod per node                     |
| `Job`                   | Run-to-completion workloads          |
| `CronJob`               | Scheduled workloads                  |
| `Service`               | Service discovery and load balancing |
| `Ingress`               | External HTTP/HTTPS routing          |
| `NetworkPolicy`         | Network security                     |
| `PersistentVolume`      | Storage resource                     |
| `PersistentVolumeClaim` | Storage request                      |
| `StorageClass`          | Dynamic storage provisioning         |
| `ConfigMap`             | Configuration management             |
| `Secret`                | Sensitive configuration              |
| `ServiceAccount`        | Pod identity                         |
| `Role / ClusterRole`    | Authorization                        |
| `Namespace`             | Logical isolation                    |

---

# 🔑 Final Takeaways

* Kubernetes objects define the desired state of applications and infrastructure
* Kubernetes continuously reconciles actual state with desired state
* Pods are the foundational execution unit
* Higher-level controllers provide orchestration capabilities around Pods
* Different object categories solve different infrastructure problems:

  * workloads
  * networking
  * storage
  * configuration
  * security
  * isolation

---
