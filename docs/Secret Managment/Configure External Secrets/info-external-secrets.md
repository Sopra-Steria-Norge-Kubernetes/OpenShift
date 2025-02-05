---
Author: Oskar Marthinussen, Marcus Notø
Title: Configuring External Secrets
Version: 1.0.0
externally-exposed: true
--- 

# External Secrets

## Introduction
External Secrets in OpenShift allow applications to access sensitive data stored outside the OpenShift cluster while still allowing Kubernetes resources to use them. External secrets address several limitations of OpenShift Secrets:

* **Secure Encoding:** Unlike OpenShift Secrets, external secrets are stored securely, making them Git-friendly. They require both system compromises for access.
* **Secure Encoding**: Unlike OpenShift Secrets, external secrets are stored securely, making them Git-friendly. They require both system compromises for access.
* **Automated Rotation:** External solutions automate secret rotation, enhancing security without manual intervention.

External Secrets work by setting up a connection to your external Key Management System (KMS) with a resource called a SecretStore. When a developer wants to extract a secret from the KMS, it creates an ExternalSecret resource in OpenShift. This Object will, through the SecretStore, extract the information in the KMS and create an encrypted OpenShift secret object in your cluster. The ExternalSecret resource can then be stored in Git to ensure Continuous Delivery (CD) for your applications. Below is a diagram to illustrate how the External Secrets work:

![developer_external_secret.png](../../img/Secret%20Managment/developer_external_secret.png)

The rest of this guide will focuses on how to use Azure KeyVault as a KMS, but external secrets supports various external secret providers. For more information about External Secrets see the [official guide](https://external-secrets.io/latest/).

## Configure External Secrets with Azure Key Vault for your tenant




## Creating the External Secret

<!-- ## Configuring External Secrets with Azure Key Vault for your tenant

### Pre-requisites
To get external secrets to work with Azure Key Vault you need have certain permissions and configure certain Azure resources:

* **Azure Permissions**: An Azure account with permissions to manage secrets within Azure KeyVault. 
   
* **OpenShift Permissions**: Access to an OpenShift account and permissions to manage resources within a specific tenant.

* **Azure Tenant ID**: Obtain this ID as it’s essential for integrating OpenShift and Azure KeyVault.

* **Service Account Access**: Set up a service account with read permissions to Azure KeyVault. 
   
* **KeyVault Credentials Secret**: Create this secret in the namespace to store Azure KeyVault access credentials. 


### Setting up Secret Store for your tenant
In OpenShift, the secret store resource defines the connection details for external secret storage platforms, such as Azure KeyVault. Azure KeyVault contains credentials, tenant ID, and the KeyVault URL, allowing OpenShift applications to fetch and use secrets from Azure securely.

The recommended way of setting up the secret store is through the OpenShift Tenant.
The tenant form is configured to derive the KeyVault name from the namespace in which it’s deployed. 
For instance, for a tenant named `tenant` with `test` and `dev` environments, secret stores will be established in each environment, connected to `tenant-test.vault.azure.net` and `tenant-dev.vault.azure.net`, respectively. This deployment approach guarantees that every environment within the tenant possesses its dedicated secret store, enhancing security and organization.


To deploy a secret store with the tenant form, include the following parameters in the file (same indent as namespace and environments):

```yaml title="Setting up secret store"
namespace:
  name: <tenant-name>
.
.
.
external_secrets:
  azure_tenant_id: <Azure-Tenant-ID>
  keyvault_credentials:
    client_id: <Client-ID>
    client_secret: <Client-Secret>
```

Replace `<Azure-Tenant-ID>`, `<Client-ID>`, and `<Client-Secret>` with your actual values. The `<Client-ID>` and `<Client-Secret>` have to be encrypted with `kubeseal`. This can be done by following this user guide: [Encrypting secrets with Kubeseal](encrypting-secret-with-kubeseal.md).


#### Deploying a custom Secret Store
If there is need to create a custom Secret Store this can be done by creating it your self. The secret store resource definition needs to be created and added to the `resources` section of your application's `kustomization.yml` file. This action will deploy the secret store directly to the application's environment. Below is a sample syntax for defining the secret store resource:

```yaml
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

Ensure to replace placeholders like `<tenant_name>`, `<env>`, `<Azure-Tenant-ID>`, and `<Azure_KeyVault_Vault_URL>` with your specific values. 

## Creating the External Secret
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
    kind: SecretStore
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