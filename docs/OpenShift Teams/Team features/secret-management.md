# Secret Management Feature

The secret managment feature is used to create global ClusterSecretStores in the team namespace, which can be utilized by multiple tenants that are a member of the team where the ClusterSecretStore is defined.

You can read more in depth about this feature by following this link: [Secret Managment](../../OpenShift%20Tenants/Tenant%20features/external-secrets.md)

```yaml
secret_management:
  external_secrets:
    enable: false
    tenant_id: ""
    team_secretstores: 
    - name: ""
      keyvault_url: ""
      client_id: "" # namespace encrypted values
      client_secret: "" # namespace encrypted values
```

## In-depth description of parameters

| <div style="width:140px">**Variable**</div>         | **Description**                                                                                                     | **Example**                                | **Type**                  | **Default Value**  |
|----------------------|---------------------------------------------------------------------------------------------------------------------|--------------------------------------------|---------------------------|------------|
| Secret Management              |                                                                                                                     |                                            |                           |
`external_secrets`              |                                                                                                                     |                                            |                           |
| `enable`            | 	Enable secret management feature                               | True / False  | Boolean                    | false |
| `tenant_id`            | Tenant ID for your Azure subscription                                        | d93d3d23-50e3-46db-b3ad-8c6c281b431e | String                    | "" |
`team_secretstore`              |                                                                                                                     |                                            |          list[]                 |
| `name`            | name of the secret                               | my-secret | String                    | "" |
| `keyvault_url`            | The URL to the Azure Key Vault you want to use                               |  | String                    | "" |
| `client_id`            | Username for Azure Key Vault (ServicePrinciple), which is encrypted as a sealed secret                               |           | Kubeseal encrypted String                    | "" |
| `client_secret`            | Allows ArgoCD to sync an ApplicationSet even if it results in an empty application                               |           | Kubeseal encrypted String                    | "" |