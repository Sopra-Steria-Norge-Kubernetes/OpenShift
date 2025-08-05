# Secret Management in an OpenShift Team

The secret management feature in the team chart is used to create global `ClusterSecretStores` within the team namespace. These stores can be utilized by all tenants assigned to the team.

You can read more in depth about this feature by following this link: [Secret Managment with External Secrets](../../About%20Container-Platform-as-a-Service/Service%20Breakdown/Secret%20Management/External%20Secrets/Introduction.md)



## Setting up ClusterSecretStore
To create a `ClusterSecretStore` using the team chart, configure the following parameters:

=== "Configuration Template"
    ```yaml
    secret_management:
      external_secrets:
        cluster_secret_store: 
        - name: <cluster_secret_store_name>
          tenant_id: <azure_tenant_id> 
          keyvault_url: <keyvault_url> 
          client_id: <encrypted_client_id>          # Encrypted with SealedSecret 
          client_secret: <encrypted_client_secret>  # Encrypted with SealedSecret
    ```
=== "Example"
    ```yaml
    secret_management:
      external_secrets:
        cluster_secret_stores: 
        - name: cluster-secret-store
          tenant_id: 8f3c5b3a-12d4-4e9f-9b92-7f04d2c44abc
          keyvault_url: https://team-poseidon.vault.azure.net/
          client_id: AgAlMkJ1FkdAaFFebrbwMsadZTdlz3BgP2dtsI3FZJmIl3McPD[...]
          client_secret: AgB4MfXJu6oX4I3F+5JG1hSFHCnTtq9IdgdfhaL1Aw0HbX[...]
    ```

!!! Notes
    `name` is used as a suffix to generate the final name of the `ClusterSecretStore`, which follows the format `<team_name>-<name>`, for example: `team-poseidon-cluster-secret-store`.

## Encrypting Key Vault Credentials

To allow OpenShift to authenticate with Azure Key Vault, the credentials from the App Registration must be encrypted before being stored in the Git repository. This is done using `SealedSecrets`, which ensures that only the `SealedSecrets controller` running in the OpenShift cluster can decrypt the values.

You can encrypt the credentials using the `scripts/encrypt_client_credentials.sh` script in your tenant repository, or follow [this guide](../../About%20Container-Platform-as-a-Service/Service%20Breakdown/Secret%20Management/Sealed%20Secrets/encrypting-secret-with-sealed-secrets.md) to perform the encryption manually.

Additional details on `SealedSecrets` can be found [here](../../About%20Container-Platform-as-a-Service/Service%20Breakdown/Secret%20Management/Sealed%20Secrets/Introduction.md).

## In-depth Description of parameters

The table below provides detailed descriptions of each variable available in the `secret_management.external_secrets` configuration.

### ClusterSecretStore parameters

| <div style="width:300px">**Variable**</div>                                 | <div style="width:200px">**Descriptopn**</div>                                                                 | <div style="width:300px">**Example**</div>                                 | **Type**                 | **Default Value** |
|---------------------------------------------|---------------------------------------------------------------------------------|---------------------------------------------|--------------------------|-------------------|
| `cluster_secret_stores`                     | A list of ClusterSecretStore entries used to connect to one or more Azure Key Vaults shared across environments | –                                           | list                     | `[]`              |
| `cluster_secret_stores[].name`              | Suffix used to generate the full `ClusterSecretStore` name in the format `<tenant_name>-<name>` | `gitops`                                               | string                   | `""`              |
| `cluster_secret_stores[].tenant_id`         | Azure Tenant ID used to access the Key Vault                                    | `d93d3d23-50e3-46db-b3ad-8c6c281b431e`      | string                   | `""`              |
| `cluster_secret_stores[].keyvault_url`      | URL of the Azure Key Vault instance                                             | `https://poseidon1.vault.azure.net/`        | string                   | `""`              |
| `cluster_secret_stores[].client_id`         | Azure App Registration Client ID, encrypted using SealedSecrets                | `AgAlMkJ1FkdAaFFebrbwMsadZTdlz3BgP2dtsI3FZJmIl3McPD[...]`                                           | sealed string            | `""`              |
| `cluster_secret_stores[].client_secret`     | Azure App Registration Client Secret, encrypted using SealedSecrets           | `AgB4MfXJu6oX4I3F+5JG1hSFHCnTtq9IdgdfhaL1Awsdfs0HbX[...]`                                           | sealed string            | `""`              |

