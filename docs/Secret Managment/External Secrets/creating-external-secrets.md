# Creating the External Secret with Azure Key Vault
To create a secret from Azure Key Vault you need to create the custom external secret resource in OpenShift. This resource interacts with the secret store to access secrets stored in Azure KeyVault and then creates an equivalent secret within OpenShift, making the sensitive data accessible to applications.

```yaml title="Defining an external secret"
apiVersion: external-secrets.io/v1beta1
kind: ExternalSecret
metadata:
  name: <name_of_external_secret>
  namespace: <tenant_name>-<env>
spec:
  refreshInterval: 10s
  secretStoreRef:
    kind: SecretStore # This value can also be ClusterSecretStore if you want multiple namespaces
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


## Example of a Completed External Secret Configuration

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
kind: SecretStore # This value can also be ClusterSecretStore if you want multiple namespaces
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

### ExternalSecret Object

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
    kind: SecretStore # This value can also be ClusterSecretStore if you want multiple namespaces
    name: demo-secret-store
  target:
    name: openshift-secret
    creationPolicy: Owner
  data:
  - secretKey: secret-key
    remoteRef:
      key: azure-secret
```

### Resulting OpenShift Secret

The configuration above will result in the creation of the following OpenShift secret:

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: openshift-secret
  namespace: demo-dev
  [...]
data:
  secret-key: bXlzZWNyZXQ=     # Decoded value: mysecret
type: Opaque
```

### Summary

In this configuration:

- The Azure secret named `azure-secret` has a value of `mysecret`.
- The SecretStore object is configured to access this secret.
- The ExternalSecret object fetches the secret from Azure and creates a corresponding secret in OpenShift named `openshift-secret`.