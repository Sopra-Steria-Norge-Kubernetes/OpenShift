# Backup using Commvault

## What is Commvault?

Commvault is a backup and recovery solution that enables organizations to protect, manage, and recover workloads across cloud, on-premises, and hybrid environments. It provides external backup capabilities for OpenShift clusters and workloads, ensuring data resiliency and disaster recovery readiness.

## What is backup?
Backup in OpenShift refers to the practice of creating copies of your application’s data and configuration, ensuring that you can restore your system to a previous state in case of data loss, corruption, or disaster.

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

This configuration automatically applies backup policies to your tenant based on the selected schedules. The backup team will handle the creation and management of these policies in the Commvault Command Center.

### Option 2: Label Individual Resources (Per-PVC)

If you want to back up only specific Persistent Volume Claims (PVCs) instead of the entire tenant, you can label them individually using the OpenShift CLI.

To assign a PVC to a specific backup policy, run:

```bash
oc label pvc <pvc-name> -n <namespace> commvault_volume_backup_daily=true
```

Replace `daily` with `weekly` or `monthly` depending on your backup schedule requirements.

#### Example
> **Note:**  
>  If you opt for labeling individual PVCs, it is important to label them using GitOps. If the resource is deleted, the labels will not automatically reappear.

Labeling can be done manually using this command; however, it's recommended to add the label in your GitOps repository so the label is managed as code and will be reapplied if the resource is recreated.

```bash
oc label pvc my-database-pvc -n my-app commvault_volume_backup_daily=true
```

## In-depth description of Commvault parameters

In the table below, there is a more detailed description of each variable in the `backup.commvault` feature:

| <div style="width:155px">**Variable**</div> | **Description** | **Example** | **Type** | **Default Value** |
|---|---|---|---|---|
| `daily` | Enable daily backup schedule for the tenant | true | Boolean | false |
| `weekly` | Enable weekly backup schedule for the tenant | true | Boolean | false |
| `monthly` | Enable monthly backup schedule for the tenant | false | Boolean | false |

## Backup Operations

Once the cluster and labels are configured, backups are performed automatically according to the configured schedules. 

### Perform a Restore

To restore data from a Commvault backup, contact the OpenShift administrator and specify which tenant or individual PVC you want restored. 


## Further reading

To learn more about backup procedures and related configurations, please refer to the following resources:

- [Commvault Documentation](https://documentation.commvault.com/)