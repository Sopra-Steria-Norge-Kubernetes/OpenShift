# Backup

## What is Backup?

Backup in OpenShift refers to the practice of creating copies of your application’s data and configuration, ensuring that you can restore your system to a previous state in case of data loss, corruption, or disaster.


## How to configure Backup

To configure backups for your OpenShift tenant using our Helm chart, include the following YAML configuration in your Helm values file:

``` yaml
...
  backup:
    backuplabel: <Label name you want to use for backups>
    take_backup: <Boolean if backup should be taken or not>
    schedules: 
      - schedule: <Schedule for backups to be taken in cron format (e.g. "30 * * * *")>
        name: <Name of schedule> 
        ttl: <Time until a backup is deleted in format XXXhYYmZZs e.g. 24h0m0s >
... 
```

By setting these parameters, you can automate the process of taking regular backups, specifying the frequency, naming, and retention of each backup. This ensures that your data is consistently protected and can be restored as needed. 

## In-depth description of parameters

In the table below, there is a more detailed description of each variable in the `backup` feature:


| <div style="width:155px">**Variable**</div>                     | **Description**                                           | **Example**                | **Type**  | **Default Value**  |
|----------------------------------|-----------------------------------------------------------|----------------------------|-----------|---------|
| `backuplabel`             | The label to set for backups. If this label is not set, a backup of your applications will not be taken. It needs to be on the format `Key: value` | "backupresource: true"| String   | "" |
| `take_backup`             | Used to specify whether backups should be taken or not in the Tenant.  | true     | Boolean   | false |
| `schedules[]`      | A list of backup schedules that will be implemented for the tenant |  | List    | "" |
| `schedules[].schedule`      | The schedule at which backups should be taken in cron time format | "0 0 * * *"             | String    | "" |
| `schedules[].name`          | The name of the backup schedule. It is prefixed with the name of the tenant. If the tenant name is `poseidon1` and this variable is set to `daily`, the backup schedule will be called `poseidon1-daily`                         | "daily"            | String    | "" |
| `schedules[].ttl`       | The time until the backup is deleted. It is in the format of `XXXhYYmZZs`, where `XXX` is the number of hours, `YY` is the number of minutes, and `ZZ` is the number of seconds. For example, `24h0m0s` would indicate that the backup should be deleted after 24 hours.| "48h0m0s"                 | String    | "" |

## Further reading

To learn more about our backup procedures and how the restore process works, please refer to the following resources.

- [Backup Persistent Volumes using VolumeSnapshots](../../../About%20Container-Platform-as-a-Service/Service%20Breakdown/Backup/backup-persistent-volumes-using-volumesnapshots.md)
- [Backup process for tenants using Velero](../../../About%20Container-Platform-as-a-Service/Service%20Breakdown/Backup/backup-process-for-tenants-using-velero.md)