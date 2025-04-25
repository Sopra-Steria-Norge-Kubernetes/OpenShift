# Observability Feature

With observability you can enable a team grafana instance that uses the openshift credentials for login. This grafana instance will automaticly have datasources from all tenants managed by the team unless specificly disabled in the tenant definitions values yaml. grafana datasources from a tenant is default enabled. 

The grafana instance must have an admin group. it does not require editor group. but can be specified if multiple access levels is wanted.

```yaml
observability:
  grafana_instance: <Enable grafana instance in team namespace (true/false). default false>  
  grafana_admin: <AD Group for grafana admin>
  grafana_editor: <AD Group for editors, can be left blank and everyone will be editor>
```

## In-depth description of parameters

| <div style="width:140px">**Variable**</div>         | **Description**                                                                                                     | **Example**                                | **Type**                  | **Default Value**  |
|----------------------|---------------------------------------------------------------------------------------------------------------------|--------------------------------------------|---------------------------|------------|
| Observability              |                                                                                                                     |                                            |                           |
| `grafana_instance`            | 	                               | True / False  | Boolean                    | false |
| `grafana_admin`            | 	AD Group for grafana admin                               | my-admin-group  | String                    | "" |
| `grafana_editor`            |     AD Group for editors, can be left blank and everyone will be editor                                    | my-editor-group | String                    | "" |
