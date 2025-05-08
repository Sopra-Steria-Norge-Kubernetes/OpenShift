# Secret Management Feature

The secret managment feature in the team chart is used to create global ClusterSecretStores in the team namespace, which can be utilized by multiple tenants that are a member of the team where the ClusterSecretStore is defined.

You can read more in depth about this feature by following this link: [Secret Managment with External Secrets](../../OpenShift%20Tenants/Tenant%20features/external-secrets.md)

!!! note
    When creating a ClusterSecretStore the name of the resource will consist of the team name and the `name` field in the secret_management configuration. The result will be: `team.name`-`secret_management.external_secrets.team_secretstores.name`.

    It is important to remember the name of the ClusterSecretStore as you need to refer to the ClusterSecretStore resource when creating an external secret.

To create a ClusterSecretStore through the team setup, you must configure the following parameters:

```yaml
secret_management:
  external_secrets:
    team_secretstores: 
    - name: <ClusterSecretStore name. Team name will be prefix and then this name>
      tenant_id: <AZURE_TENANT_ID - Tenant ID of your organizations Azure tenant>
      keyvault_url: <Url to Azure Key Vault - https://AZURE_KEY_VAULT_URL> 
      client_id: <Sealed Secret App Registration Credentials - SealedSecret_CLIENT_ID> 
      client_secret: <Sealed Secret - App Registration Credentials -SealedSecret_CLIENT_SECRET> 
```

## In-depth description of parameters

| <div style="width:140px">**Variable**</div>         | **Description**                                                                                                     | **Example**                                | **Type**                  | **Default Value**  |
|----------------------|---------------------------------------------------------------------------------------------------------------------|--------------------------------------------|---------------------------|------------|
| Secret Management              |                                                                                                                     |                                            |                           |
`external_secrets`              |                                                                                                                     |                                            |                           | 
`team_secretstore`              |                                                                                                                     |                                            |          list[]                 |
| `name`            | name of the ClusterSecretStore that will be added behind the team name parameter                               | my-secret | String                    | "" |
| `tenant_id`            | Tenant ID for your Azure subscription                                        | d93d3d23-50e3-46db-b3ad-8c6c281b431e | String                    | "" |
| `keyvault_url`            | The URL to the Azure Key Vault you want to use                               |  | String                    | "" |
| `client_id`            | Username for Azure Key Vault (ServicePrinciple), which is encrypted as a sealed secret                               |           | Kubeseal encrypted String                    | "" |
| `client_secret`            | Allows ArgoCD to sync an ApplicationSet even if it results in an empty application                               |           | Kubeseal encrypted String                    | "" |