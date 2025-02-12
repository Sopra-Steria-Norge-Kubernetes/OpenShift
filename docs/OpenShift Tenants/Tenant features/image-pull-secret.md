# Image Pull Secret

## What is a Image Pull Secret?

In OpenShift, an `image pull secret` is a Kubernetes secret used to authenticate and pull container images from a private container registry. It stores credentials (username, password, or token) that allow OpenShift to securely access images from remote registries like Azure Container Registry (ACR), Docker Hub, AWS Elastic Container Registry (ECR), Google Container Registry (GCR), and others

## How to Configure an Image Pull Secret

The `image_pull_secret` feature contains the information OpenShift needs to securly access a remote registry in the form of a Sealed Secret dockercofigjson object. It also contains the namespace environment which defines where the image pull secret is to be created. To configure a image pull secret using a Helm chart, include the following YAML configuration in your Helm values file:


```yaml
...
  image_pull_secret:
    enable: false # boolean - true/false
    secrets:
    - dockerconfigjson: <dockerconfigjson_SEALED_SECRET> # SealedSecret dockerconfigjson object 
      environment: <environment> # environment to place image_pull_secret
...
```

By configuring these parameters, you can customize the image pull secret to securly access your specific image registry form your wanted namespace environment. To see how to configure a namespace environment visit: [Tenant Feature - Namespace](./namespace.md)

## In-depth description of parameters

In the table below, you can find a more detailed description of each variable in the `image_pull_secret` feature:

| <div style="width:205px">**Variable**</div>           | **Description**                                                                      | **Example**                                | **Type**   | **Default Value**
|:------------------------------|---------------------------------------------------------------------------------------|--------------------------------------------|:------------|--------|
| `enable`                         | When using image pull secret change this value to enable: True                                              | false                                  | boolean     | false |
| `secrets.dockerconfigjson`                | Sealed Secret dockerconfigjson object containing credentials to acces remote image registry                                | AgA2Z8F5bN... (long encrypted string) | String     | "" |
| `environment`                | Environment image pull secret will be deployed in                                 | "preprod"                   | String     | "" |

For documentation on how you can use your image pull secret to use an image from your private registry in a deployment see the official documentation at: [**Kubernetes Documentation**](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/#create-a-pod-that-uses-your-secret) 