Types of shared volumes:
* Temporary storage (emptyDir): Shared between containers, got deleted when Pod dies.
* Persistent storage (PersistentVolumeClaim): PersistentVolumeClaim (PVC) provides the storage that can survive Pod deletion. This storage can be shared one multiple containers of one pod or optionally it can be shared between Pods as well, depending on the `storage backend system` and `access mode`.

    Case 1: ReadWriteOnce (RWO) — most common
    * Only one Pod can mount it at a time
    * NOT shared across Pods simultaneously
    * Can be shared between containers of the same Pod

    ```text
    Pod A ✔ uses PVC
    Pod B ❌ cannot use same PVC at same time
    ```

    Case 2: ReadWriteMany (RWX)
    * Multiple Pods can mount it simultaneously i.e. shared across Pods
    * Also, can be shared between containers of the same Pod

    ```
    Pod A ─┐
       ├── shared PVC
    Pod B ─┘
    ```

    Case 3: ReadOnlyMany (ROX)
    * Multiple Pods can mount it simultaneously
    * Read-only access across Pods
    * Can be shared between containers of the same Pod