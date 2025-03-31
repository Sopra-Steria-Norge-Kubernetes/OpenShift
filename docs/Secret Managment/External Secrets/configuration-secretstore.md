# Deploying Secret Store with Azure Key Vault for your tenant

## Prerequisites

- Configured an App Registration
- App Registartion has the role 'Key Vault Secret User'
- Configured an App Registartion Secret (Client Secret)

## Deploying a custom Secret Store

Before deploying a custom secret store you need to fulfill these prerequsits: [**Prerequsits for Secret Store**](../../OpenShift%20Tenants/Tenant%20features/external-secrets.md)

If there is need to create a custom Secret Store this can be done by creating it yourself. The secret store resource definition needs to be created and added to the `resources` section of your application's `kustomization.yml` file. This action will deploy the secret store directly to the application's environment.

- Below is a sample syntax for defining a SecretStore resource:

```yaml title="secret store resource layout"
apiVersion: external-secrets.io/v1alpha1
kind: SecretStore
metadata:
  name: <tenant_name>-secret-store
  namespace: <tenant_name>-<env>
spec:
  provider:
    azurekv:
      authSecretRef:
        clientId:
          key: ClientID
          name: keyvault-credentials
        clientSecret:
          key: ClientSecret
          name: keyvault-credentials
      authType: ServicePrincipal
      tenantId: <Azure-Tenant-ID>
      vaultUrl: <Azure_KeyVault_Vault_URL>
```

- Below is a sample syntax for defining a ClusterSecretStore resource (If you want to have your secret store available cluster wide over multiple namespaces):

```yaml title="cluster secret store resource layout"
apiVersion: external-secrets.io/v1alpha1
kind: ClusterSecretStore
metadata:
  name: <tenant_name>-secret-store
  namespace: <tenant_name>-<first_env>
spec:
  provider:
    azurekv:
      tenantId: <AZURE_TENANT_ID>
      vaultUrl: <AZURE_KEY_VAULT_URL>
      authSecretRef:
        clientId:
          name: keyvault-credentials
          key: ClientID
          namespace: <namespace>-<first_env>
        clientSecret:
          name: keyvault-credentials
          key: ClientSecret
          namespace: <namespace>-<first_env> # <first_env> value should be the first element in the environments list in the tenant
```

Ensure to replace placeholders like `<tenant_name>`, `<env>`, `<AZURE_TENANT_ID>`, `<AZURE_KEY_VAULT_URL>`, `<namespace>`, and `<first_env>` with your specific values. 

Next step: [**Creating External Secrets**](creating-external-secrets.md)

<!-- ## Creating the External Secret
To create a secret from Azure Key Vault you need to create the custom resource external secret in OpenShift.  It interacts with the secret store to access secrets stored in Azure KeyVault and then creates an equivalent secret within OpenShift, making the sensitive data accessible to applications.

```yaml title="Defining an external secret"
apiVersion: external-secrets.io/v1beta1
kind: ExternalSecret
metadata:
  name: <name_of_external_secret>
  namespace: <tenant_name>-<env>
spec:
  refreshInterval: 10s
  secretStoreRef:
    kind: ClusterSecretStore
    name: <tenant_name>-secret-store
  target:
    name: <secret_name_in_ocp>
    creationPolicy: Owner
  data:
  - secretKey: <key_definition_in_secret>
    remoteRef:
      key: <key_ref_in_azure>
```

Below is the explanation of the different variables:

- **Name of External Secret** (`<name_of_external_secret>`): This is the unique identifier for the ExternalSecret object within OpenShift.
- **Namespace** (`<tenant_name>-<env>`): Replace with the specific tenant and environment where the secret is to be deployed.
- **Refresh Interval:** This determines how often the external secret syncs with Azure KeyVault to ensure updated data accessibility.
- **Secret Name in OCP** (`<secret_name_in_ocp>`): The designated name for the secret within OpenShift post-import.
- **Key Definition in Secret** (`<key_definition_in_secret>`): This key is used within OpenShift for referencing the secret’s value.
- **Key Reference in Azure** (`<key_ref_in_azure>`): The name of the secret as stored in Azure KeyVault.

This ExternalSecret object connects to Azure KeyVault through the specified SecretStore, fetching the secret identified by `<key_ref_in_azure>`. It then creates or updates a secret named `<secret_name_in_ocp>` in OpenShift, storing the fetched value under `<key_definition_in_secret>`. 

When `creationPolicy: Owner` is set, the ExternalSecret object "owns" the resulting secret within OpenShift. In this ownership model, if the ExternalSecret object is deleted, the associated secret within OpenShift is also automatically deleted. On the other hand, a `creationPolicy: Merge` would imply that the secret remains even after deleting the ExternalSecret object, enhancing data persistence.


### Example of a Completed External Secret Configuration

Below is a comprehensive example that includes an Azure secret, a SecretStore object, and an ExternalSecret object. This real-world example demonstrates how these elements interconnect.

#### Azure Secret

A secret is stored in Azure KeyVault with the following attributes:

- **Vault URL**: `https://demo-dev.vault.azure.net/`
- **Secret Name**: `azure-secret`
- **Secret Value**: `mysecret`

#### SecretStore Object

The SecretStore object is defined within OpenShift to reference the Azure secret. Here is its configuration:

```yaml
apiVersion: external-secrets.io/v1beta1
kind: SecretStore
metadata:
  name: demo-secret-store
  namespace: demo-dev
spec:
  provider:
    azurekv:
      authSecretRef:
        clientId:
          key: ClientID
          name: keyvault-credentials
        clientSecret:
          key: ClientSecret
          name: keyvault-credentials
      authType: ServicePrincipal
      tenantId: dne39jdh-slik-f9wj-bcdd-sdofjs5dlkfj
      vaultUrl: https://demo-dev.vault.azure.net
```

#### ExternalSecret Object

An ExternalSecret object is used to fetch the secret from Azure using the defined SecretStore. Its configuration is as follows:

```yaml
apiVersion: external-secrets.io/v1beta1
kind: ExternalSecret
metadata:
  name: openshift-external-secret
  namespace: demo-dev
spec:
  refreshInterval: 10s
  secretStoreRef:
    kind: SecretStore
    name: demo-secret-store
  target:
    name: openshift-secret
    creationPolicy: Owner
  data:
  - secretKey: secret-key
    remoteRef:
      key: azure-secret
```

#### Resulting OpenShift Secret

The configuration above will result in the creation of the following OpenShift secret:

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: openshift-secret
  namespace: demo-dev
  [...]
immutable: false
data:
  secret-key: bXlzZWNyZXQ=     # Decoded value: mysecret
type: Opaque
```

#### Summary

In this configuration:

- The Azure secret named `azure-secret` has a value of `mysecret`.
- The SecretStore object is configured to access this secret.
- The ExternalSecret object fetches the secret from Azure and creates a corresponding secret in OpenShift named `openshift-secret`. -->