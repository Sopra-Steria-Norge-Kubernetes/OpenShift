# External Secrets

!!! Info
    This feature is only supported for Azure Key Vault, but contact your OpenShift administrator for additional KMS support.

    *[KMS]: Key Management System


## What are External Secrets?

External Secrets in OpenShift allow you to securely store and manage sensitive information, such as API keys, passwords, and certificates, outside of your OpenShift cluster. This approach enhances security by integrating with external secret management services, ensuring that sensitive data is not exposed within the cluster configuration or codebase. By using external secrets, you can maintain better control and auditing of secret access and updates.


## How to configure External Secrets

To configure external secrets for your OpenShift tenant using our Helm chart, include the following YAML configuration in your Helm values file:

``` yaml
...
  external_secrets:
    azure_tenant_id: <Azure-Tenant-ID>
    keyvault_credentials:
      client_id: <Client-ID for Azure Key Vault (ServicePrinciple)>
      client_secret: <Client-Secret for Azure Key Vault (ServicePrinciple)>
... 
```

By setting these parameters, you can securely connect your OpenShift cluster to Azure Key Vault, managing secrets externally while providing the necessary credentials to access these secrets. This configuration ensures that sensitive information is handled securely and efficiently.

**Read the full configuration guide here:**

- [Configuring External Secrets guide](../../Secret%20Managment/configuring-external-secrets.md)


## In-depth description of parameters

In the table below, there is a more detailed description of each variable in the `backup` feature:


| <div style="width:255px">**Variable**</div>                         | **Description**                                  | **Example**                | **Type**  | **Default Value**  |
|--------------------------------------|--------------------------------------------------|----------------------------|-----------|--------|
| `azure_tenant_id`                    | The Azure Tenant ID that your organisation uses for storing its Azure Key Vaults  |d93d3d23-50e3-46db-b3ad-8c6c281b431e      | String    | "" |
| `keyvault_credentials.client_id`      | Username for Azure Key Vault (ServicePrinciple), which is encrypted as a sealed secret | | Kubeseal encrypted String    | "" |
| `keyvault_credentials.client_secret`  | Password for Azure Key Vault (ServicePrinciple), which is encrypted as a sealed secret | | Kubeseal encrypted String    | "" |