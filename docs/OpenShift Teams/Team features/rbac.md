# RBAC Feature

RBAC is used to grant specific Azure Entra ID groups access to OpenShift roles across the team namespace and all associated tenant namespaces.

```yaml
rbac:
  team_edit: ""
  team_view: ""
  team_monitoring_edit: ""
  team_monitoring_view: ""
  team_security: ""
```

### Keycloak

An alternative to Azure is provided by using `keycloak.enabled: true`.

Uses the same parameter for groups (`team_`).

```yaml
...
  rbac:
  team_edit: ""
  team_view: ""
  team_monitoring_edit: ""
  team_monitoring_view: ""
  team_security: ""

    keycloak:
      enabled: false
      url: ""
      realm: "" # Global variable - per customer/team
      # If the service account/user is in another realm.  Change this.
      # Default: `master`
      loginRealm: "master"
...
```

## In-depth description of parameters

| <div style="width:160px">**Variable**</div> | **Description**                   | **Example**                    | **Type** | **Default Value** |
|---------------------------------------------|-----------------------------------|--------------------------------|----------|-------------------|
| `rbac`                                      |                                   |                                |          |                   |
| `team_edit`                                 | AD Group for team edit access     | "team-2s-edit"                 | String   | ""                |
| `team_view`                                 | AD Group for team view access     | "team-2s-view"                 | String   | ""                |
| `team_monitoring_edit`                      | AD Group for grafana admin access | "team-2s-monitoring-edit"      | String   | ""                |
| `team_monitoring_view`                      | AD Group for grafana view access  | "team-2s-monitoring-view"      | String   | ""                |
| `team_security`                             | AD Group for ACS access           | "team-2s-security"             | String   | ""                |
| `keyclaok.enable`                           | Use Keycloak                      | "true"                         | Boolean  | `false`           |
| `keycloak.url`                              | FQDN to Keycloak instance         | "https://keycloak.example.com" | String   | ""                |
| `keycloak.realm`                            | Realm where groups reside         | "customer"                     | String   | ""                |
| `keycloak.loginRealm`                       | Alternative realm used for login  | "master"                       | String   | `master`          |
