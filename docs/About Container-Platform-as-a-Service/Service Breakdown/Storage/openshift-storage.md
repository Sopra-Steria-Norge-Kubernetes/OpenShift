# OpenShift Storage

This page covers how to provision and manage persistent storage in OpenShift using OpenShift Data Foundation (ODF).

It includes how to choose the right StorageClass, create PersistentVolumeClaims (PVCs), and use snapshots for backup and restore.

**Official Documentation:** [OpenShift Storage Overview](https://docs.openshift.com/container-platform/latest/storage/index.html)

## StorageClasses
A StorageClass defines how storage is provisioned in OpenShift, enabling dynamic creation of persistent volumes with specific performance and access settings.

Most storage is backed by Ceph (via OpenShift Data Foundation) on SSD media. The key difference between the Ceph-backed StorageClasses is **how the data is replicated**:

- Replication across multiple datacenters protects data even if an entire site becomes unavailable, at the cost of some extra write latency.
- Replication within a single datacenter (or no replication at all) is faster and cheaper, but offers little or no protection if that datacenter or its underlying disks fail.

Customers should pick a StorageClass based on how critical and how reproducible their data is, balancing resilience against performance/cost.

### Available StorageClasses

| StorageClass | Access Modes | Backend | Replication / Resiliency | Performance | Typical Use Case |
|---|---|---|---|---|---|
| **`ceph-storagecluster-ceph-rbd`** | RWO | Ceph RBD (block) | Replicated across 3 datacenters | High (SSD) | Default choice for most workloads — databases and application data that need high availability |
| **`ceph-rbd-2-site`** (default) | RWO | Ceph RBD (block) | Replicated across 2 datacenters | High (SSD) | Workloads that need cross-site resilience without the overhead of full 3-site replication |
| **`ocs-storagecluster-ceph-fs`** | RWO/RWX | CephFS (shared filesystem) | Replicated across the Ceph storage cluster | High (SSD) | Shared/multi-pod access to the same volume (e.g. shared config or upload directories) |
| **`ocs-storagecluster-ceph-non-resilient-rdb`** | RWO | Ceph RBD (block) | No cross-site replication — data lives in a single datacenter only | High (SSD) | Ephemeral, replaceable, or non-critical data that doesn't need full resiliency (e.g. caches, scratch space, easily reproducible data) |
| **`nfs-hdd-rwx`** | RWX | External NFS (Isilon) | Handled by the external Isilon appliance | Lower (HDD) | Bulk/archival shared file storage where high performance isn't required |

In addition to the above, Object Bucket Claims (OBCs) are available via `ocs-storagecluster-ceph-rgw` and `openshift-storage.noobaa.io` for object storage use cases.

**Recommendation:** Use `ceph-storagecluster-ceph-rbd` for most workloads unless a specific access mode (RWX), resiliency, or cost/performance trade-off calls for one of the other classes.


## Persistent Volumes

Create storage by defining a PVC and binding it to your application. The PV is automatically provisioned.

1. **Create PVC:**

    ```yaml
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: my-app-pvc
    spec:
      accessModes:
        - ReadWriteOnce
      resources:
        requests:
          storage: 10Gi
      storageClassName: ceph-storagecluster-ceph-rbd
    ```

2. **Bind to Pod:**

    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
      name: my-app-pod
    spec:
      containers:
        - name: my-app-container
          image: my-app-image:latest
          volumeMounts:
            - name: my-app-volume
              mountPath: /path/to/mount
      volumes:
        - name: my-app-volume
          persistentVolumeClaim:
            claimName: my-app-pvc
    ```

## Backup and Restore

Bellow is a link to a documentation for the backup and restore process using pvc´s:

[Guide for backup and restore](../Backup/backup-persistent-volumes-using-volumesnapshots.md)
