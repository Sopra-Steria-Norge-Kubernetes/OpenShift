# Deploying Secret Store with Azure Key Vault for your tenant

## Prerequisites
Ensure you have the following:

* An Azure Key Vault instance
* An Azure App Registration with associated ClientID and ClientSecret
* The App Registration must be assigned the `Key Vault Secrets User` role for the relevant Key Vault
* Your Azure Tenant ID

## Deploying a custom Secret Store

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

