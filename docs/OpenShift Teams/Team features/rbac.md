# RBAC Feature

RBAC is used to provide wanted access to specific groups in openshift that spans over the team namespace and tenant namespaces of the team. 

```yaml
rbac:  
  team_edit: ""
  team_view: ""
  team_monitoring_edit: ""
  team_monitoring_view: ""
  team_security: ""
```

## In-depth description of parameters

| <div style="width:160px">**Variable**</div>               | **Description**                                                                                                     | **Example**                                | **Type**                  | **Default Value**  |
|---------------------------|---------------------------------------------------------------------------------------------------------------------|--------------------------------------------|---------------------------|------------|
| `rbac`              |                                                                                                                     |                                            |                           |
| `team_edit`                       | AD Group for team edit access                                | "team-2s-edit"                      | String                          | "" |
| `team_view`                       | AD Group for team view access                                 | "team-2s-view"                     | String                          | "" |
| `team_monitoring_edit`            | AD Group for grafana admin access                             | "team-2s-monitoring-edit"          | String                          | "" |
| `team_monitoring_view`            | AD Group for grafana view access                              | "team-2s-monitoring-view"          | String                          | "" |
| `team_security`                   | AD Group for ACS access                                       | "team-2s-security"                 | String                          | "" |