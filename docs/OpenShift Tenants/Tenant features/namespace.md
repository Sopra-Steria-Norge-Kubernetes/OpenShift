# Namespace

## What is a Namespace?

In OpenShift, `namespaces` provide a mechanism for isolating groups of resources within a single cluster. Names of resources need to be unique within a namespace, but not across namespaces. Namespaces allow for better organization and management of resources, enabling different teams or projects to share a single cluster without interfering with each other.

## How to order a Namespace

The `namespace` feature contains information about Tenant namespaces and Tenant resources.To order a namespace using a Helm chart, include the following YAML configuration in your Helm values file:

``` yaml
...
  namespace:
    name: <Tenant Name>
    description: <Tenant Description>
    displayName: <Display name of the tenant>
    use_egress_firewall: <Use egress firewall to limit egress traffic from tenant namespaces>
    enable_global_egress_IPs: <Use egress firewall IPs defined in a global values file>
    enable_tooling: <Creates a tooling namespace>
    deploy_grafana: <Deploy Grafana instance in tooling namespace>
    storage:
      enable_custom_storageclass: <Enable option to have custom storageclasses>
    limits:
      enable: true
      memory: <Memory limits of all tenants>
      cpu: <Combined CPU limits of all namespaces in tenant>
    requests:
      enable: false
      memory: <Memory requests of all tenants>
      cpu: <Combined CPU requests of all namespaces in tenant>
    container_limitrange:
      enable: true
      limits:
        memory: <Default Memory limitfor containers in all tenant namspace>
        cpu: <Default cpu limit for containers in all tenant namspaces>
      requests:
        memory: <Default Memory request for containers in all tenant namspace>  
        cpu: <Default cpu request for containers in all tenant
    labels:
      custom_labels:
        <key>: <value>
        <key>: <value>
...
```

By configuring these parameters, you can customize the namespace to meet your specific requirements, ensuring proper resource allocation and management for your tenant’s applications.

To create a specific environment such as `<namespace>-test` within the namespace, ensure to configure the **Environment** section according to the instructions found [here](../Tenant%20features/environments.md).

## Default Container limits
By default, containers in the tenant have the following resource limits:

- **CPU**: 250m (0.25 cores)

- **Memory**: 1 GiB

These default limits can be modified by adjusting the parameters in `namespace.container_limitrange` .
Maximum Resource Limits:

- **Maximum CPU per standard Worker Node**: 8 cores (excluding management overhead)

- **Maximum Memory per standard Worker Node**: 32 GiB (excluding management overhead)

To increase these maximum limits, Sopra Steria can provision larger worker nodes for your tenant. 


## In-depth description of parameters

In the table below, you can find a more detailed description of each variable in the `namespace` feature:

| <div style="width:205px">**Variable**</div>           | **Description**                                                                      | **Example**                                | **Type**   | **Default Value**
|:------------------------------|---------------------------------------------------------------------------------------|--------------------------------------------|:------------|--------|
| `name`                         | Base name of tenant.                                                                 | Poseidon1                                  | String     | "" |
| `description`                | A description annotation  under each tenant namespace                                 | " This is a test tenant used for testing" | String     | "" |
| `displayName`                | Displayname given to each openshift namespace/project                                 | "poseidon1-application1"                   | String     | "" |
| `use_egress_firewall`         | To use egress firewall to limit egress traffic from tenant namespaces.                | true                                       | Boolean    | true |
| `enable_global_egress_IPs`    | Use egress firewall IPs defined in a global values file                              | false                                       | Boolean    | false |
| `enable_tooling`              | Creates a tooling namespace which is needed for certain applications such as Grafana | true                                       | Boolean    | true |
| `deploy_grafana`              | Deploys a grafana instance in the tooling namespace                                      | true                                       | Boolean    | true |
| `storage.enable_custom_storageclass`              | Enable option to have custom storageclasses                                      | true                                       | Boolean    | false |
| `limits.memory`               | Combined memory limit for all tenant namespaces. The memory resource is measured in bytes. Memory can be expressed as a plain or fixed-point integer with one of these suffixes: E, P, T, G, M, K, Ei, Pi, Ti, Gi, Mi, Ki.    | 1Gi                                        | String/Int | 1Gi |
| `limits.cpu`                 | Combined cpu limit for all tenant namespaces. Fractional values are allowed. A Container that requests 500m CPU is guaranteed half as much CPU as a Container that requests 1 CPU. You can use the suffix m to mean milli. For example 100m CPU, 100 milliCPU, and 0.1 CPU are all the same. A precision finer than 1m is not allowed.                                         | 1                                          | String/Int | 1 |
| `requests.memory`               | Combined memory requests for all tenant namespaces. The memory resource is measured in bytes. Memory can be expressed as a plain or fixed-point integer with one of these suffixes: E, P, T, G, M, K, Ei, Pi, Ti, Gi, Mi, Ki.    | 1Gi                                        | String/Int | 1Gi |
| `requests.cpu`                 | Combined cpu requests for all tenant namespaces. Fractional values are allowed. A Container that requests 500m CPU is guaranteed half as much CPU as a Container that requests 1 CPU. You can use the suffix m to mean milli. For example 100m CPU, 100 milliCPU, and 0.1 CPU are all the same. A precision finer than 1m is not allowed.                                         | 1                                          | String/Int | 1 |
| `container_limitrange.limits.memory` | Default memory limits for containers in all tenant namespaces. This can be overridden by specifying values in the container configuration. The memory resource is measured in bytes. Memory can be expressed as a plain or fixed-point integer with one of these suffixes: E, P, T, G, M, K, Ei, Pi, Ti, Gi, Mi, Ki.                            | 64Mi                                       | String/Int | 1Gi |
| `container_limitrange.limits.CPU`     | Default CPU limits for containers in all tenant namespaces. This can be overridden by specifying values in the container configuration. Fractional values are allowed. A Container that requests 0.5 CPU is guaranteed half as much CPU as a Container that requests 1 CPU. You can use the suffix m to mean milli. For example 100m CPU, 100 milliCPU, and 0.1 CPU are all the same. A precision finer than 1m is not allowed.                                  | 100m                                       | String/Int | 250m |
| `container_limitrange.requests.memory`     | Default memory request for containers in all tenant namespaces. This can be overridden by specifying values in the container configuration. Same values as for `container_limitrange.limits.memory` is allowed.                              |100m   | String/Int | "" |
| `container_limitrange.requests.CPU`     |  Default CPU request for containers in all tenant namespaces. This can be overridden by specifying values in the container configuration. Same values as for `container_limitrange.limits.CPU` is allowed.                              |100m   | String/Int | "" |
| `labels.custom_labels`              | Add custom labels to all namespaces in a tenant                                      | test_label: label                                       | key: value    | "" |

## Further reading
[Kubernetes Offical Documentation - Namespaces](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/)