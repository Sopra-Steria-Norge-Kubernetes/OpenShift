# Observability Feature

## Introduction

With observability you can enable a team grafana instance that uses the openshift credentials for login. This grafana instance will automaticly have datasources from all tenants managed by the team unless specificly disabled in the tenant definitions values yaml. grafana datasources from a tenant is default enabled. 

The grafana instance must have an admin group. it does not require editor group. but can be specified if multiple access levels is wanted.

```yaml
observability:
  grafana_instance: <Enable grafana instance in team namespace (true/false). default false>  
  grafana_admin: <AD Group for grafana admin>
  grafana_editor: <AD Group for editors, can be left blank and everyone will be editor>
```

By enabling `grafana_instance: true` you'll get a grafana instance with a set of default resource management dashboards. To read more about these dashboards visit this page.

- [Team Resource Dashboards](../../Additional%20Documentation/Resource%20Management/team-resource-dashboards.md)

!!! Info
    Extract you team Grafana URL with the following command
    ```bash
    $ oc get route -n <TEAM-NAME> -o jsonpath='{range .items[*]}{.spec.host}{"\n"}{end}'
    ```

## In-depth description of parameters

| <div style="width:140px">**Variable**</div>         | **Description**                                                                                                     | **Example**                                | **Type**                  | **Default Value**  |
|----------------------|---------------------------------------------------------------------------------------------------------------------|--------------------------------------------|---------------------------|------------|
| Observability              |                                                                                                                     |                                            |                           |
| `grafana_instance`            | 	                               | True / False  | Boolean                    | false |
| `grafana_admin`            | 	AD Group for grafana admin                               | my-admin-group  | String                    | "" |
| `grafana_editor`            |     AD Group for editors, can be left blank and everyone will be editor                                    | my-editor-group | String                    | "" |
