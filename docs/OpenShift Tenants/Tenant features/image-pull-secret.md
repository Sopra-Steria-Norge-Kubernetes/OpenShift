# Image Pull Secret

## What is a Image Pull Secret?

In OpenShift, an image pull secret is a Kubernetes secret used to authenticate and pull container images from a private container registry. It stores credentials (username, password, or token) that allow OpenShift to securely access images from registries such as Azure Container Registry (ACR), Docker Hub, AWS ECR, and Google Container Registry (GCR).

## Recommended implementation: Use External Secrets

The preferred method for managing image pull secrets is now through the [External Secrets integration](secret-management.md). This method allows secrets to be securely and automatically synced from Azure Key Vault into OpenShift using `ClusterSecretStore` configurations, reducing the need to manually manage SealedSecrets.

We recommend using the External Secrets method unless you have a specific requirement for `SealedSecrets`.

## Configure Image Pull Secret with SealedSecret

If you still wish to use SealedSecrets, you can configure an image pull secret by including the following configuration in your Helm values file:

```yaml
...
  image_pull_secret:
    enable: false # boolean - true/false
    secrets:
    - dockerconfigjson: <dockerconfigjson_sealed_secret> # SealedSecret dockerconfigjson object 
      environment: <environment> # Namespace where the image pull secret will be created
...
```


## In-depth description of parameters

In the table below, you can find a more detailed description of each variable in the `image_pull_secret` feature:

| <div style="width:205px">**Variable**</div>           | **Description**                                                                      | **Example**                                | **Type**   | **Default Value**
|:------------------------------|---------------------------------------------------------------------------------------|--------------------------------------------|:------------|--------|
| `enable`                         | When using image pull secret change this value to enable: True                                              | false                                  | boolean     | false |
| `secrets.dockerconfigjson`                | Sealed Secret dockerconfigjson object containing credentials to acces remote image registry                                | AgA2Z8F5bN... (long encrypted string) | String     | "" |
| `environment`                | Environment image pull secret will be deployed in                                 | "preprod"                   | String     | "" |

For documentation on how you can use your image pull secret to use an image from your private registry in a deployment see the official documentation at: [**Kubernetes Documentation**](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/#create-a-pod-that-uses-your-secret) 