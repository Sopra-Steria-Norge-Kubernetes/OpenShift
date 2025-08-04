# OpenShift Storage

This page covers how to provision and manage persistent storage in OpenShift using OpenShift Data Foundation (ODF).

It includes how to choose the right StorageClass, create PersistentVolumeClaims (PVCs), and use snapshots for backup and restore.

**Official Documentation:** [OpenShift Storage Overview](https://docs.openshift.com/container-platform/latest/storage/index.html)

## StorageClasses
A StorageClass defines how storage is provisioned in OpenShift, enabling dynamic creation of persistent volumes with specific performance and access settings.

In a typical setup with ODF, the following StorageClasses are available:

* **`ocs-storagecluster-ceph-rbd`** (default): RWO filesystem volumes and RWO/RWX block volumes
* **`ocs-storagecluster-cephfs`**: RWO and RWX filesystem volumes for multi-pod access
* **`ocs-storagecluster-ceph-rgw`**: Object Bucket Claims (OBCs) with Ceph
* **`openshift-storage.noobaa.io`**: Object Bucket Claims (OBCs) with NooBaa

**Recommendation:** Use filesystem storage classes for most workloads. Store object data outside the cluster when possible.


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
      storageClassName: ocs-storagecluster-ceph-rbd
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
Create point-in-time snapshots of PVCs and restore to new PVCs.

1. Navigate to Storage -> PersistentVolumeClaims in the OpenShift console.
![image.png](../../img/Storage/storage1.png)

2. Click the three dots to the right of the pvc you want to snapshot.
![image.png](../../img/Storage/storage2.png)

3. Proceed with default settings, and click "Create"
![image.png](../../img/Storage/storage3.png)

4. Navigate to Storage -> VolumeSnapshots to find your recently created snapshot.
![image.png](../../img/Storage/storage4.png)

5. To restore a PVC from the snapshot click the three dots next to the right of the VolumeSnapshot.
![image.png](../../img/Storage/storage5.png)

6. Proceed with default settings to create a new pvc.
![image.png](../../img/Storage/storage6.png)

7. Find your restored snapshot under Storage -> PersistentVolumeClaims.
![image.png](../../img/Storage/storage7.png)


