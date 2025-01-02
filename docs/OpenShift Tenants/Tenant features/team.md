---
Author: Rune Landa Gjerde
Title: Order OpenShift tenant team
Version: 1.0
externally-exposed: true
--- 
# Team

## What is a team?

A `team` is a common definition for tenants that belongs to the same "team", configuration here is heavily dependent on configuration done on the team level wich you can find more info about on the  [team-definitions](../OpenShift%20Tenants/Tenant%20features/Tenant%20team/team-introduction.md) page.

## How to configure team

Within the tenant definitions yaml file you can add the following:

```yaml
  team:
    team_name: <team name>
    grafana_datasource: true
```
This will connect the tenant to the correct team. 
grafana_datasource is enabled by default, this will add a rolebinding to team service account allowing team admins to extract the token to connect grafana datasources manually.