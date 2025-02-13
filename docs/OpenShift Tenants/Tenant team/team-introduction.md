---
Author: Rune Landa Gjerde
Title: Order OpenShift tenant team
Version: 1.0
externally-exposed: true
--- 
# Team

## What is a team in regards to tenants?

Team overlay will let you group multiple tenants within one team and ease the administration of tenants that is managed by the same development team. it allows creation of a team grafana for observability.
When team is created in openshift, a new namespace wit the team name will be created, in this namespace all common secrets and service accounts will be created. 

## How to configure team

Within the team definitions yaml file you can configure the following:

```yaml
team:
  name: <name of team>

rbac:  
  team_admin: <AD Group for team admin>

observability:
  grafana_instance: false
  grafana_admin: <AD Group for grafana admin>
  grafana_editor: <AD Group for editors, can be left blank and everyone will be editor>
```
# Features

```yaml
team:
  name: <name of team>
```
The team name chosen in team definitions value yaml is used in the tenant definitions yaml for connect tenants to the team. it is important that name is exactly the same.

```yaml
rbac:  
  team_admin: <AD Group for team admin>
````
rbac is used to give one specific group in openshift admin access over the team namespace. 

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
With observability you can enable a team grafana instance that uses the openshift credentials for login. This grafana instance will automaticly have datasources from all tenants managed by the team unless specificly disabled in the tenant definitions values yaml. grafana datasources from a tenant is default enabled. 

The grafana instance must have an admin group. it does not require editor group. but can be specified if multiple access levels is wanted. 