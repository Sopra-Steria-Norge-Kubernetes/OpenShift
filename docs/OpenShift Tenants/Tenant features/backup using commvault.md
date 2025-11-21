# Backup using Commvault

## What is Commvault?

Commvault is an enterprise-grade backup and recovery solution that enables organizations to protect, manage, and recover workloads across cloud, on-premises, and hybrid environments. It provides external backup capabilities for OpenShift clusters and workloads, ensuring data resiliency and disaster recovery readiness.


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

```bash
oc label pvc my-database-pvc -n my-app commvault_volume_backup_daily=true
```

> **Note:**  
>  If you opt for labeling individual PVCs, it is important to label them using GitOps. If the resource is deleted, the labels will not automatically reappear.

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

To restore data from a Commvault backup:

1. Open the **Commvault Command Center**
2. Select the appropriate **Restore Point**
3. Choose the resource type to restore (e.g., Namespace, PVC, Pod, ConfigMap, Secret)
4. Review the restore scope and click **Start Restore**
5. Monitor the restore operation until completion
6. Verify that all resources have been restored successfully in OpenShift

## Verification and Validation

After completing a restore operation, validate the outcome to ensure data consistency and application functionality:

- Confirm that all restored resources are present in OpenShift using `oc get` commands
- Verify that your applications start and operate normally
- Review Commvault logs and alerts in the Command Center for any warnings or errors
- Notify the backup team once validation is complete

## Troubleshooting

| **Issue** | **Solution** |
|---|---|
| Backup jobs not appearing in Commvault | Verify that tenant configuration is correctly applied and that backup policies are created in the Command Center |
| PVC not being backed up | Check resource labeling matches the configured backup policy labels (e.g., `commvault_volume_backup_daily=true`) |
| Restore operation failed | Verify the restore point is valid and contains the required resources; check Commvault logs for error details |
| Missing backups | Ensure firewall rules allow Commvault VSA to reach OpenShift API; confirm backup policies are scheduled and active |

## Further reading

To learn more about backup procedures and related configurations, please refer to the following resources:

- [Backup](./backup.md)
- [Commvault Documentation](https://documentation.commvault.com/)