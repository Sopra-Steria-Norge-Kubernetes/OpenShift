# Secret Managment with External Secrets

!!! Info
    This feature is only supported for Azure Key Vault, but contact your OpenShift administrator for additional KMS support.

    *[KMS]: Key Management System


## What is Secret Managment with External Secrets?

External Secrets in OpenShift allow you to securely store and manage sensitive information, such as API keys, passwords, and certificates, outside of your OpenShift cluster. This approach enhances security by integrating with external secret management services, ensuring that sensitive data is not exposed within the cluster configuration or codebase. By using external secrets, you can maintain better control and auditing of secret access and updates.


## How to configure External Secrets

To configure your Azure and OpenShift environment for secret managment you need to follow these steps:

1. Azure configuration - Setup App Registration and Azure Key Vault and give the App Registartion the 'Key Vault Secret User' role
2. OpenShift Configuration - Add the App Registartion credentials and Azure Key Vault url to the OpenShift Tenant (or Team Overlay)

To configure secret managment for your OpenShift tenant using our Helm chart, include the following YAML configuration in your Helm values file:

``` yaml
...
secret_management:
  external_secrets:
    enable: false # boolean - treu/false
    tenant_id: <AZURE_TENANT_ID> # Tenant ID of your organizations Azure tenant
    tenant_secretstores: 
    - name: # Secret name
      keyvault_url: <https://AZURE_KEY_VAULT_URL> # Url to Azure Key Vault
      client_id: <CLIENT_ID> # Sealed Secret - Azure Key Vault (ServicePrinciple)
      client_secret: <CLIENT_SECRET> # Sealed Secret - Azure Key Vault (ServicePrinciple)
...
```

By setting these parameters, you can securely connect your OpenShift cluster to Azure Key Vault, managing secrets externally while providing the necessary credentials to access these secrets. This configuration ensures that sensitive information is handled securely and efficiently.

**Read the full configuration guides here:**

- [**Introduction to Eternal Secrets**](../../Secret%20Managment/Configure%20External%20Secrets/info-external-secrets.md)
- [**Configuring SecretStore with Azure Key Vault**](../../Secret%20Managment/Configure%20External%20Secrets/configuration-secretstore.md)
- [**Create External Secrets with Azure Key Vault**](../../Secret%20Managment/Configure%20External%20Secrets/creating-external-secrets.md)

## In-depth description of parameters

In the table below, there is a more detailed description of each variable in the `backup` feature:


| <div style="width:255px">**Variable**</div>                         | **Description**                                  | **Example**                | **Type**  | **Default Value**  |
|--------------------------------------|--------------------------------------------------|----------------------------|-----------|--------|
| `enable`                    | When using secret managment change this value to enable: True                     | false     | Boolean  | false   |
| `tenant_id`                    | The Azure Tenant ID that your organisation uses for storing its Azure Key Vaults  |d93d3d23-50e3-46db-b3ad-8c6c281b431e      | String    | "" |
| `tenant_secretstores.name`  | name of the secret | my_secret | string    | "" |
| `tenant_secretstores.url`  | The URL to the Azure Key Vault you want to use | | string    | "" |
| `tenant_secretstores.client_id`      | Username for Azure Key Vault (ServicePrinciple), which is encrypted as a sealed secret | | Kubeseal encrypted String    | "" |
| `tenant_secretstores.client_secret`  | Password for Azure Key Vault (ServicePrinciple), which is encrypted as a sealed secret | | Kubeseal encrypted String    | "" |