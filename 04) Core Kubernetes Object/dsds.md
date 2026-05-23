Examples:

* containerd
* CRI-O
* Docker (older setups)

Responsibilities:

* pull container image
* create containers
* start containers

### 🌐 Container creation flow

```text
kubelet
    ↓
container runtime
    ↓
pull nginx image
    ↓
create container
    ↓
start container
```


# 🌐 Why Pause Container Exists

The pause container holds:

* Pod network namespace
* Pod lifecycle namespace

All application containers join this shared namespace.

---

# 🌐 Pod Networking Model

```text id="y6m’wini"
Pod
 ├── pause container
 ├── nginx container
 └── sidecar container
```

All containers share:

* same Pod IP
* same localhost
* same port space

---
