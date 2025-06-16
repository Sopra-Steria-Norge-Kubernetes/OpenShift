---
Author: Oskar Marthinussen, Marcus Notø
Title: Configuring External Secrets
Version: 1.0.0
externally-exposed: true
--- 

# Introduction External Secrets

External Secrets let you securely manage sensitive data such as API keys, passwords, and certificates by integrating your OpenShift tenant with a Key Management Service like Azure Key Vault. Secrets are fetched from the external provider and synced into OpenShift as native Secret objects, allowing applications to consume them without embedding sensitive data in configuration files. With `ClusterSecretStore` and Azure Key Vault, you can centrally manage secrets across multiple namespaces, reduce duplication, simplify access control, and support enterprise compliance with less administrative overhead.

Below is a diagram to illustrate how the external secrets and cluster secret store works:
![developer_external_secret.png](../../../img/Secret Managment/cluster-secret-store-v3.drawio.png)

## Cluster Secret Store with team integration

With team integration, a `ClusterSecretStore` can be created at the team level and made accessible to all tenants managed by that team. This setup allows the team to centrally manage the credentials used by individual `SecretStore` resources across multiple tenant namespaces.

As illustrated below, the blue boxes represent secrets and credentials associated with the `ClusterSecretStore`.

![developer_external_secret.png](../../../img/Secret Managment/secret_management.png)

!!! Info
    `ExternalSecret 1–3` are defined in each tenant's namespace and reference the shared `ClusterSecretStore` to retrieve credentials from Azure Key Vault. 

This model ensures a consistent and scalable approach to secret management across environments while reducing duplication and simplifying credential lifecycle management.



## External Secrets Resource

In OpenShift, External Secrets allow applications to use secrets stored in external systems without exposing sensitive data in cluster configuration. They address several limitations of native OpenShift Secrets:

* **Secure Storage:** Secrets remain stored securely in an external backend. Gaining access requires compromising both the OpenShift cluster and the external provider, making it Git-friendly and more secure.
* **Automated Rotation:** Many supported providers offer automatic secret rotation, reducing the need for manual updates and improving security posture.

## How it works
1. A SecretStore resource defines the connection to the external KMS (e.g. Azure Key Vault).
2. A developer creates an ExternalSecret resource in OpenShift, referencing the SecretStore.
3. The ExternalSecret fetches the desired secret from the external provider and creates a Kubernetes Secret within the namespace.
4. The ExternalSecret resource itself can be stored in Git, enabling secure, version-controlled secret management as part of your GitOps pipeline.

The diagram below illustrates this process:
![developer_external_secret.png](../../../img/Secret%20Managment/developer_external_secret.png)

!!! info
    You can choose between creating a <ins>SecretStore</ins> or a <ins>ClusterSecretStore</ins>:

    * **ClusterSecretStore**: Available cluster-wide, can be used across multiple namespaces.
    * **SecretStore**: Scoped to a single namespace, only available within that namespace.

This documentation focuses on Azure Key Vault, but External Secrets supports many providers. See the [official documentation](https://external-secrets.io/latest/) for more details.

## Configure secret store with Azure Key Vault for your tenant

For configuring a secret store with Azure Key Vault you have to do the following:

- [**SecretStore Configuration**](configuration-secretstore.md) - Setup App Registartion and Azure Key Vault and give the App Registration the 'Key Vault Secrets User' role

## Creating external secrets with Azure Key Vault

- [**External Secret Creation**](creating-external-secrets.md) - Add the App Registration credentials and Azure Key Vault url to the OpenShift Tenant (or Team Overlay) and create your external secret