# Observability Feature

With observability you can enable a team grafana instance that uses the openshift credentials for login. This grafana instance will automaticly have datasources from all tenants managed by the team unless specificly disabled in the tenant definitions values yaml. grafana datasources from a tenant is default enabled. 

The grafana instance must have an admin group and is defined in the [RBAC](./rbac.md) section with the `team_monitoring_edit` field. If multiple access levels is wanted it is possible to define a Viewer group with the `team_monitoring_view` field, but this is not required.

```yaml
observability:
  grafana_instance: <Enable grafana instance in team namespace (true/false). default false>  
```

## In-depth description of parameters

| <div style="width:140px">**Variable**</div>         | **Description**                                                                                                     | **Example**                                | **Type**                  | **Default Value**  |
|----------------------|---------------------------------------------------------------------------------------------------------------------|--------------------------------------------|---------------------------|------------|
| Observability              |                                                                                                                     |                                            |                           |
| `grafana_instance`            | 	                               | True / False  | Boolean                    | false |
