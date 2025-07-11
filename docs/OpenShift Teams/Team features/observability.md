# Observability Feature

With observability you can enable a team grafana instance that uses the openshift credentials for login. This grafana instance will automaticly have datasources from all tenants managed by the team unless specificly disabled in the tenant definitions values yaml. grafana datasources from a tenant is default enabled. 

The grafana instance must have an admin group. it does not require editor group. but can be specified if multiple access levels is wanted.

```yaml
observability:
  grafana_instance: <Enable grafana instance in team namespace (true/false). default false>  
  grafana_admin: <AD Group for grafana admin>
  grafana_editor: <AD Group for editors, can be left blank and everyone will be editor>
```

## Grafana dashboards
By enabling `grafana_instance`, your team will receive a Grafana instance preloaded with a set of predefined dashboards. These dashboards provide an overview of the tenants within your team, including their namespaces, workloads, and containers. They also include cost metrics, showing both per-tenant breakdowns and the total cost for the entire team.

### Team Overview - All Tenants
This dashboards provides and overview over all the tenants in the team. You will get a cost overview over you hard request quota across tenants, aswell as a cost overview per tenant. The dashboard also highlights unused quota and identifies which tenants are underutilizing their allocated resources the most.

### Resource Optimization - Per Tenant
This dashboard provides a detailed view of resource optimization and cost per tenant. You can select a specific tenant using the variable at the top of the dashboard to filter the metrics accordingly. The dashboard shows the number of namespaces, total requested quota, and the portion that is underutilized. It also includes a table listing all workloads within the selected tenant, _excluding Jobs and CronJobs_, highlighting opportunities to optimize resource requests for CPU and memory.

All recommendations are based on actual resource usage measured over a defined period (X), using the 95th percentile by default. You can customize the percentile, vCPU price, and overhead percentage through dashboard variables to match your team’s preferences.


## In-depth description of parameters

| <div style="width:140px">**Variable**</div>         | **Description**                                                                                                     | **Example**                                | **Type**                  | **Default Value**  |
|----------------------|---------------------------------------------------------------------------------------------------------------------|--------------------------------------------|---------------------------|------------|
| Observability              |                                                                                                                     |                                            |                           |
| `grafana_instance`            | 	                               | True / False  | Boolean                    | false |
| `grafana_admin`            | 	AD Group for grafana admin                               | my-admin-group  | String                    | "" |
| `grafana_editor`            |     AD Group for editors, can be left blank and everyone will be editor                                    | my-editor-group | String                    | "" |
