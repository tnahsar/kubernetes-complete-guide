
---

# 📘 Chapter 18: Volumes, PV & PVC

---

## 🔹 1️⃣ Why Volumes Exist

As discussed in Chapter 17:

* Pods are ephemeral → container storage is temporary
* Volumes provide **a way to persist data beyond Pod lifecycle**
* Volumes abstract the underlying storage (host disk, cloud storage, network storage)

---

## 🔹 2️⃣ What Is a Volume?

A **Volume** in Kubernetes:

* Is a directory accessible by a Pod
* Lives as long as the Pod exists
* Can be shared among containers in the same Pod
* Can be backed by various storage types

### Example:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-volume
spec:
  containers:
    - name: app
      image: nginx
      volumeMounts:
        - mountPath: /usr/share/nginx/html
          name: app-data
  volumes:
    - name: app-data
      emptyDir: {}
```

* `emptyDir` → volume type
* `volumeMounts` → where the volume appears in the container filesystem

---

## 🔹 3️⃣ Types of Volumes (Important)

| Volume Type                       | Description                                          |
| --------------------------------- | ---------------------------------------------------- |
| emptyDir                          | Ephemeral volume, cleared on Pod deletion            |
| hostPath                          | Mount host node directory (not recommended for prod) |
| configMap                         | ConfigMaps as files                                  |
| secret                            | Secrets as files                                     |
| persistentVolumeClaim             | Mount a PV (persistent storage)                      |
| NFS, iSCSI, Ceph, AWS EBS, GCP PD | Network-attached storage, survives Pod restarts      |

> Most production apps use **PersistentVolumeClaim** backed by PV (cloud storage, NFS, EBS, etc.)

---

## 🔹 4️⃣ PersistentVolume (PV)

### What It Is

* A **cluster resource** representing real storage
* Can be **pre-provisioned** or **dynamically provisioned**
* Exists independently of Pods

### Example PV

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-demo
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  hostPath:
    path: /mnt/data
```

* `capacity` → how much storage
* `accessModes` → who can read/write
* `reclaimPolicy` → what happens when PV is released

---

## 🔹 5️⃣ PersistentVolumeClaim (PVC)

### What It Is

* A **Pod requests storage** via PVC
* Kubernetes **binds PVC → PV** automatically
* PVC is **Pod-level abstraction** over PV

### Example PVC

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-demo
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi
```

* Pod does **not care which PV** it uses
* Kubernetes finds **matching PV** automatically

---

## 🔹 6️⃣ Using PVC in a Pod

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-using-pvc
spec:
  containers:
    - name: app
      image: nginx
      volumeMounts:
        - mountPath: /usr/share/nginx/html
          name: storage
  volumes:
    - name: storage
      persistentVolumeClaim:
        claimName: pvc-demo
```

* Pod mounts the PVC → accesses persistent storage
* Pod can be deleted/recreated → **data persists**

---

## 🔹 7️⃣ Access Modes (Very Important)

| Mode          | Meaning                               |
| ------------- | ------------------------------------- |
| ReadWriteOnce | Pod can read/write, single node       |
| ReadOnlyMany  | Pod(s) can read, multiple nodes       |
| ReadWriteMany | Pod(s) can read/write, multiple nodes |

> For most cloud PVs: **ReadWriteOnce** is standard

---

## 🔹 8️⃣ Reclaim Policies (What Happens to Data)

| Policy  | Behavior                         |
| ------- | -------------------------------- |
| Retain  | Keep data even if PVC is deleted |
| Delete  | Delete the underlying storage    |
| Recycle | Basic cleanup (deprecated)       |

> Choose carefully based on **prod vs dev requirements**

---

## 🔹 9️⃣ Mental Model

* PV → **Storage in the cluster** (like a disk)
* PVC → **Pod asks for storage** (like a request form)
* Pod → **Mounts PVC** → uses storage

> This separates **storage management** from **application deployment** — very powerful for scaling and portability

---

## 🔹 10️⃣ Key Takeaways (Interview-Friendly)

* **Pods are ephemeral** → need Volumes for persistence
* **PersistentVolume (PV)** → cluster-level storage
* **PersistentVolumeClaim (PVC)** → Pod requests storage
* PV + PVC decouples **storage from compute**
* Access modes & reclaim policies control sharing & lifecycle

---