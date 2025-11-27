# Backup using Commvault

## What is Commvault?

Commvault is a backup and recovery solution that enables organizations to protect, manage, and recover workloads across cloud, on-premises, and hybrid environments. It provides external backup capabilities for OpenShift clusters and workloads, ensuring data resiliency and disaster recovery readiness.

## How to configure Commvault Backup

Commvault backup can be configured for your OpenShift tenant using one of two methods:

### Option 1: Configure via Helm Values (Tenant-wide)

To enable Commvault backup schedules for your entire tenant, include the following YAML configuration in your Helm values file:

```yaml
...
backup:
  commvault:
    daily: <Boolean - enable daily backups>
    weekly: <Boolean - enable weekly backups>
    monthly: <Boolean - enable monthly backups>
...
```

#### Example Configuration

```yaml
...
backup:
  commvault:
    daily: true
    weekly: true
    monthly: false
...
```

This configuration automatically applies backup policies to your PVCs based on the selected schedules. The backup team will handle the creation and management of these policies in the Commvault Command Center.

### Option 2: Label Individual Resources via GitOps (Per-PVC)

If you want to back up only specific Persistent Volume Claims (PVCs) instead of the entire tenant, you can label them individually in your GitOps repository.

To assign a PVC to a specific backup policy, add the appropriate label to your PVC manifest:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-database-pvc
  namespace: poseidon-test
  labels:
    commvault_daily: "true"
spec:
```

Replace `daily` with `weekly` or `monthly` depending on your backup schedule requirements.

#### Example

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-database-pvc
  namespace: poseidon-test
  labels:
    commvault_daily: "true"
    commvault_weekly: "true"
spec:
```

> **Note:**  
> It is important to add labels through GitOps so they are managed as code. If the resource is deleted and recreated, the labels will be automatically reapplied.

## In-depth description of Commvault labels

| <div style="width:155px">**Variable**</div> | **Description** | **Example** | **Type** | **Default Value** |
|---|---|---|---|---|
| `daily` | Enable daily backup schedule for the tenant | true | Boolean | false |
| `weekly` | Enable weekly backup schedule for the tenant | true | Boolean | false |
| `monthly` | Enable monthly backup schedule for the tenant | false | Boolean | false |

## Backup Operations

Once the labels are configured, backups are performed automatically according to the configured schedules. 

### Perform a Restore

To restore data from a Commvault backup, contact the OpenShift administrator and specify which tenant or individual PVC you want restored. 


## Further reading

To learn more about backup procedures and related configurations, please refer to the following resources:

- [Commvault Documentation](https://documentation.commvault.com/)