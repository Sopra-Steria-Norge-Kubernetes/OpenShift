# Team Feature

The team name chosen in team definitions value yaml is used in the tenant definitions yaml for connect tenants to the team. it is important that name is exactly the same.

```yaml
team:
  name: <name of team>
  description: <description of team>
```

## In-depth description of parameters

| <div style="width:140px">**Variable**</div>         | **Description**                                                                                                     | **Example**                                | **Type**                  | **Default Value**  |
|----------------------|---------------------------------------------------------------------------------------------------------------------|--------------------------------------------|---------------------------|------------|
| Team              |                                                                                                                     |                                            |                           |
| `name`            | Name of your team                               | My-Team | String                    | "" |
| `description`            | Description of your team                                        | This is My-Team | String                    | "" |