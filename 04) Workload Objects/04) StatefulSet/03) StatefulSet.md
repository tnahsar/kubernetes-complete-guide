# 🗄️ Stateful Applications

Stateful applications require a unique, stable network identity and persistent storage to function correctly. 

Unlike stateless applications (which treat application instances as interchangeable), stateful application instances must retain their context and data across restarts, crashes, and updates.

---

## 🏗️ 1. StatefulSet Object

> `StatefulSet` is the workload object used to manage stateful applications. 

* `StatefulSet` manages the deployment and scaling of a set of Pods while providing guarantees about **Pod ordering**, **unique, stable network identity**, and **persistent storage** association.

## 3 Pod Ordering

* **Ordered creation of Pods**: Pods are created in a strict sequential order (0, 1, 2, ... N-1). Pod X must be fully Running and Ready before Pod X+1 is initiated.
* **Ordered termination of Pods**: During scale-down, Pods are terminated in reverse order (N-1, N-2, ... 0). Pod X must completely terminate before Pod X-1 is touched.
* **Rolling Updates**: During rolling updates, Pods are updated one at a time in reverse ordinal order (N-1 to 0).


## 🌐 Unique and stable network identity

* **Pod ordinal index**: Every Pod in a StatefulSet is assigned a sequential integer from 0 to N-1 (e.g., db-0, db-1, db-2).
* **Predictable hostnames**: The hostname of the Pod matches its name (e.g., db-0 always has the hostname db-0).
* **Headless Service**: A dedicated Service (with clusterIP: None) must accompany the StatefulSet. It configures the DNS system to return direct DNS record - `A` for individual Pods.

$$\text{pod-name}.\text{service-name}.\text{namespace}. \text{svc.cluster.local}$$

## 💾 Stable Persistent Storage

* **Volume Claim Templates**: Instead of sharing a single volume, a StatefulSet uses a volumeClaimTemplate to automatically generate unique PersistentVolumeClaims (PVCs) for each Pod instance.
* **Identity Binding**: The PVC is explicitly bound to the corresponding ordinal index (e.g., data-db-0).
* **Persistence on Deletion**: When a Pod is deleted or scaled down, its associated PVC is not automatically deleted. This protects data from accidental loss during scaling events.

---

## 🧱 2. Structural Breakdown (YAML Architecture)

The following production-ready manifest deploys a 3-node PostgreSQL cluster using a StatefulSet and an accompanying Headless Service.

```yaml
apiVersion: v1
kind: Service

metadata:
  name: postgres-hdls
  namespace: database
  labels:
    app: postgres

spec:
  clusterIP: None # Defines this as a Headless Service
  selector:
    app: postgres

  ports:
    - name: ecom-db
      port: 5432

---
apiVersion: apps/v1
kind: StatefulSet

metadata:
  name: postgres-cluster
  namespace: database

spec:
  serviceName: "postgres-hdls" # Links to the Headless Service above
  replicas: 3

  selector:
    matchLabels:
      app: postgres

  template:
    metadata:
      labels:
        app: postgres

    spec:
      containers:
        - name: postgres
          image: postgres:15-alpine

          ports:
            - containerPort: 5432
              name: ecom-db

          env:
            - name: POSTGRES_PASSWORD
              value: "SecureVaultPass99"

          volumeMounts:
            - name: pgdata
              mountPath: /var/lib/postgresql/data

  volumeClaimTemplates: # Automatically provisions isolated storage per Pod
    - metadata:
        name: pgdata

      spec:
        accessModes: ["ReadWriteOnce"]
        storageClassName: "premium-rwo"

        resources:
          requests:
            storage: 50Gi
```

---

## 💽 3. Storage Lifecycle Enforcement

Understanding data attachment during lifecycle transitions is vital for operational stability.

```text
       [ Pod db-0 ]             [ Pod db-1 ]             [ Pod db-2 ]
            │                        │                        │
       [ PVC db-0 ]             [ PVC db-1 ]             [ PVC db-2 ]
            │                        │                        │
  ┌─────────────────┐      ┌─────────────────┐      ┌─────────────────┐
  │ Persistent Vol. │      │ Persistent Vol. │      │ Persistent Vol. │
  │     (PV-001)    │      │     (PV-002)    │      │     (PV-003)    │
  └─────────────────┘      └─────────────────┘      └─────────────────┘
            │                        │                        │
      [ Reschedules ]          [ Reboots ]             [ Scaled Down ]
            │                        │                        │
  [ Connects back to ]     [ Connects back to ]     [ Volume Persists ]
  [    same storage ]      [    same storage ]      [   Data Safe!!   ]
```

---

## ⚠️ 4. Key Failure Modes & Mitigation Strategies

Stateful deployments require explicit operational rules to prevent distributed data corruption.

### 🧠 The Split-Brain Risk (At-Most-One Guarantee)

* **Problem**: During a node failure or network partition, Kubernetes control plane may think a StatefulSet Pod is dead even though it is still running and writing data on the original node. If it forces a restart the same pod on a new node while the old Pod is still writing to storage, data corruption occurs (Two Pods may write to the same storage).


* **Mitigation**: StatefulSet follows `At-Most-One` Pod semantics, meaning - Only one Pod with a specific StatefulSet identity should run at a time.

    Example:
    ```text
    mysql-0 should never run on two nodes simultaneously.
    ```

    ### ⚠️ Important Warning

    Never force-delete a StatefulSet Pod stuck in:

    * `Unknown`
    * `Terminating`

    during node failure unless the original node is safely powered off or isolated.

### 📦 PVC Orphanage Bloat

* **Problem**: When a StatefulSet is scaled down from 5 replicas → 3 replicas, extra Pods are removed but their PersistentVolumeClaims (PVCs) are usually retained.

    Example:
    ```text
    mysql-3 Pod deleted
    mysql-4 Pod deleted
    ```

    but:
    ```text
    pvc-mysql-3
    pvc-mysql-4
    ```
    may still remain in the cluster/cloud environment.

    This can lead to:
    * unused storage
    * additional cloud cost

* **Mitigation**: Use regular cleanup processes to audit unused PVCs, or configure - `persistentVolumeClaimRetentionPolicy` field in the StatefulSet to control PVC retention behavior during scale-down or deletion.

### 🚀 Scaling Bottlenecks

* **Problem**: StatefulSet creates Pods sequentially. So, for large distributed systems with huge datasets: **pod-0 → pod-1 → pod-2** scaling can become slow because each Pod waits for the previous Pod to become ready.

* **Mitigation**: For distributed applications that manage their own clustering/quorum logic, configure:
`podManagementPolicy: Parallel`. This allows Pods to start in parallel while still preserving:
* stable Pod names
* stable storage association

---