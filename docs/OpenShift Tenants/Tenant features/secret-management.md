# Secret Managment In an OpenShift Tenant

OpenShift tenants support configuring one or more `ClusterSecretStore` and `SecretStore` resources.

* A **ClusterSecretStore** is available across all namespaces within the tenant.
* A **SecretStore** is scoped to a specific namespace or environment.

Both types of stores integrate with external Key Management Services (KMS), such as Azure Key Vault, to fetch and manage secrets within OpenShift.
You can read more about how External Secrets work in [this section](../../Additional%20Documentation/Secret%20Managment/External%20Secrets/Introduction.md).

!!! Info
    OpenShift tenants are configured to use Azure Key Vault as the Key Management System (KMS). Contact your OpenShift administrator if you require support for a different KMS.


## Pre-requisites
Ensure you have the following:

* An Azure Key Vault instance
* An Azure App Registration with associated ClientID and ClientSecret
* The App Registration must be assigned the `Key Vault Secrets User` role for the relevant Key Vault
* Your Azure Tenant ID

## Setting up ClusterSecretStore
A `ClusterSecretStore` defines a connection to an Azure Key Vault and enables sharing of secrets across multiple namespaces in OpenShift. Below is an example configuration for defining a `ClusterSecretStore` in your tenant values:

=== "Configuration Template"
    ```yaml
    secret_management:
      external_secrets:
        cluster_secret_store: 
        - name: <cluster_secret_store_name>
          environment: <namespace> 
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
          environment: poseidon1-dev
          tenant_id: 8f3c5b3a-12d4-4e9f-9b92-7f04d2c44abc
          keyvault_url: https://poseidon1.vault.azure.net/
          client_id: AgAlMkJ1FkdAaFFebrbwMsadZTdlz3BgP2dtsI3FZJmIl3McPD[...]
          client_secret: AgB4MfXJu6oX4I3F+5JG1hSFHCnTtq9IdgdfhaL1Aw0HbX[...]
    ```

!!! Notes
    * `environment` refers to the environment/namespace where the Key Vault credentials (stored as an OpenShift Secret) will be placed.
    * `name` is used as a suffix to generate the final name of the `ClusterSecretStore`, which follows the format `<tenant_name>-<name>`, for example: `poseidon1-cluster-secret-store`.

## Encrypting Key Vault Credentials

To allow OpenShift to authenticate with Azure Key Vault, the credentiwwwwwwals from the App Registration must be encrypted before being stored in the Git repository. This is done using `SealedSecrets`, which ensures that only the `SealedSecrets controller` running in the OpenShift cluster can decrypt the values.

You can encrypt the credentials using the `scripts/encrypt_client_credentials.sh` script in your tenant repository, or follow [this guide](../../Additional%20Documentation/Secret%20Managment/Sealed%20Secrets/encrypting-secret-with-sealed-secrets.md) to perform the encryption manually.

Additional details on `SealedSecrets` can be found [here](../../Additional%20Documentation/Secret%20Managment/Sealed%20Secrets/Introduction.md).

## Setting up SecretStore
A `SecretStore` defines a connection to an Azure Key Vault that is scoped to a single namespace in OpenShift. This is useful when secrets should only be accessible within a specific environment rather than shared across multiple namespaces.

Below is an example configuration for adding a `SecretStore` to your tenant:

=== "Configuration Template"
    ```yaml
    secret_management:
      external_secrets:
        secret_stores:
        - name: <secret_store_name>
          environment: <namespace>
          tenant_id: <azure_tenant_id>
          keyvault_url: <keyvault_url>
          client_id: <encrypted_client_id>
          client_secret: <encrypted_client_secret>
          cluster_secret_store_ref: <cluster_secret_store_name>
    ```
=== "Example"
    ```yaml
    secret_management:
      external_secrets:
        secret_stores:
        - name: poseidon1-dev-secret-store
          environment: poseidon1-dev
          tenant_id: 8f3c5b3a-12d4-4e9f-9b92-7f04d2c44abc
          keyvault_url: https://poseidon1.vault.azure.net/
          client_id: a7b1f2c3-d4e5-678f-90ab-1cd2345ef678
          client_secret: poseidon1-client-secret
          cluster_secret_store_ref: team-poseidon-gitops
    ```

!!! Notes

    * The environment field specifies the target namespace in which the `SecretStore` and corresponding OpenShift secret will be created.
    * The `cluster_secret_store_ref` links to a previously defined `ClusterSecretStore`, which holds the encrypted credentials. It is recommended to define this in the team concept.
    * The `client_id` is provided in plain text, while `client_secret` refers to the name of the secret in Azure Key Vault. The External Secrets Operator uses this name to retrieve the value and inject it into the OpenShift Secret.


## In-depth Description of parameters

The table below provides detailed descriptions of each variable available in the `secret_management.external_secrets` configuration.

### ClusterSecretStore parameters

| <div style="width:300px">**Variable**</div>                                 | <div style="width:200px">**Descriptopn**</div>                                                                 | <div style="width:300px">**Example**</div>                                 | **Type**                 | **Default Value** |
|---------------------------------------------|---------------------------------------------------------------------------------|---------------------------------------------|--------------------------|-------------------|
| `cluster_secret_stores`                     | A list of ClusterSecretStore entries used to connect to one or more Azure Key Vaults shared across environments | –                                           | list                     | `[]`              |
| `cluster_secret_stores[].name`              | Suffix used to generate the full `ClusterSecretStore` name in the format `<tenant_name>-<name>` | `gitops`                                               | string                   | `""`              |
| `cluster_secret_stores[].environment`       | Namespace where the SealedSecret with Azure credentials will be stored          | `poseidon1-dev`                         | string                   | `""`              |
| `cluster_secret_stores[].tenant_id`         | Azure Tenant ID used to access the Key Vault                                    | `d93d3d23-50e3-46db-b3ad-8c6c281b431e`      | string                   | `""`              |
| `cluster_secret_stores[].keyvault_url`      | URL of the Azure Key Vault instance                                             | `https://poseidon1.vault.azure.net/`        | string                   | `""`              |
| `cluster_secret_stores[].client_id`         | Azure App Registration Client ID, encrypted using SealedSecrets                | `AgAlMkJ1FkdAaFFebrbwMsadZTdlz3BgP2dtsI3FZJmIl3McPD[...]`                                           | sealed string            | `""`              |
| `cluster_secret_stores[].client_secret`     | Azure App Registration Client Secret, encrypted using SealedSecrets           | `AgB4MfXJu6oX4I3F+5JG1hSFHCnTtq9IdgdfhaL1Awsdfs0HbX[...]`                                           | sealed string            | `""`              |

### SecretStore parameters
| <div style="width:300px">**Variable**</div>                                 | <div style="width:200px">**Descriptopn**</div>                                                                 | <div style="width:300px">**Example**</div>                                 | **Type**                 | **Default Value** |
|---------------------------------------------|---------------------------------------------------------------------------------|---------------------------------------------|--------------------------|-------------------|
| `secret_stores`                             | A list of SecretStore entries used to fetch secrets from Azure Key Vault scoped to a single environment | –                         | list                     | `[]`              |
| `secret_stores[].name`                      | Unique name for the SecretStore configuration                                   | `poseidon1-dev-secret-store`                       | string                   | `""`              |
| `secret_stores[].environment`               | Namespace where the resulting OpenShift secrets will be created                 | `poseidon1-dev`                             | string                   | `""`              |
| `secret_stores[].tenant_id`                 | Azure Tenant ID used to access the Key Vault                                    | `d93d3d23-50e3-46db-b3ad-8c6c281b431e`      | string                   | `""`              |
| `secret_stores[].keyvault_url`              | URL of the Azure Key Vault instance                                             | `https://poseidon1.vault.azure.net/`        | string                   | `""`              |
| `secret_stores[].client_id`                 | Azure App Registration Client ID in plain text                                  | `a7b1f2c3-d4e5-678f-90ab-1cd2345ef678`      | string                   | `""`              |
| `secret_stores[].client_secret`             | Name of the secret in Azure Key Vault that contains the actual Client Secret    | `poseidon1-client-secret`                   | string                   | `""`              |
| `secret_stores[].cluster_secret_store_ref`  | Name of the ClusterSecretStore used for authentication                          | `team-poseidon-gitops`                                    | string                   | `""`              |