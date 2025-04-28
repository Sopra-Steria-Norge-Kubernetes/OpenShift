# RBAC Feature

RBAC is used to give one specific group in openshift admin access over the team namespace. 

```yaml
rbac:  
  team_admin: <AD Group for team admin>
```

## In-depth description of parameters

| <div style="width:140px">**Variable**</div>         | **Description**                                                                                                     | **Example**                                | **Type**                  | **Default Value**  |
|----------------------|---------------------------------------------------------------------------------------------------------------------|--------------------------------------------|---------------------------|------------|
| `rbac`              |                                                                                                                     |                                            |                           |
| `team_admin`            | AD Group for team admin                               | "team-2s" | String                    | "" |
