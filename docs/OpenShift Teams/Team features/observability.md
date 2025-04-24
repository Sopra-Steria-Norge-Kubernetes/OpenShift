# Observability Feature

With observability you can enable a team grafana instance that uses the openshift credentials for login. This grafana instance will automaticly have datasources from all tenants managed by the team unless specificly disabled in the tenant definitions values yaml. grafana datasources from a tenant is default enabled. 

The grafana instance must have an admin group. it does not require editor group. but can be specified if multiple access levels is wanted.

```yaml
observability:
  grafana_instance: false
  grafana_admin: <AD Group for grafana admin>
  grafana_editor: <AD Group for editors, can be left blank and everyone will be editor>
  notification:
    name: "teams-alert"
    type: teams <Tested with slack or teams, will most likely work with any webhook based type> 
    title: Grafana Alert
    webhook_url: "" <Slack or teams workflow webhook, will most likely work with other webhooks aswell>
    message: "An alert has been triggered in Grafana"
```