# Resource Management Feature

Resource managment is used to set the cpu and memory request quotas for the team namespace. The request quotas set in this section determined the resources that will be reserved in the cluster for the team namespace.

```yaml
resource_management:
  requests:
    cpu: "200m"
    memory: "500Mi"
  storage:
    enable_custom_storageclass: false
```

## In-depth description of parameters

| <div style="width:140px">**Variable**</div>         | **Description**                                                                                                     | **Example**                                | **Type**                  | **Default Value**  |
|----------------------|---------------------------------------------------------------------------------------------------------------------|--------------------------------------------|---------------------------|------------|
| Resource Management              |                                                                                                                     |                                            |                           |
`resources.requests`              |                                                                                                                     |                                            |                           |
| `cpu`            | 	Combined cpu requests for all tenant namespaces. Fractional values are allowed. A Container that requests 500m CPU is guaranteed half as much CPU as a Container that requests 1 CPU. You can use the suffix m to mean milli. For example 100m CPU, 100 milliCPU, and 0.1 CPU are all the same. A precision finer than 1m is not allowed.                               | 1  | String/Int                    | 200m |
| `memory`            | Combined memory requests for all tenant namespaces. The memory resource is measured in bytes. Memory can be expressed as a plain or fixed-point integer with one of these suffixes: E, P, T, G, M, K, Ei, Pi, Ti, Gi, Mi, Ki.                                        | 1Gi | String/Int                    | 500Mi |
Storage              |                                                                                                                     |                                            |                           |
| `enable_custom_storageClass`            | Allows ArgoCD to sync an ApplicationSet even if it results in an empty application                               | True / False | Boolean                    | false |
