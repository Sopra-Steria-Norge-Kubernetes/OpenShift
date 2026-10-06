# Resource Management Feature

Resource management sets the CPU and memory request quota for the team namespace. The quota limits the combined requests of all workloads in that namespace; it does not reserve capacity for monitoring components.

```yaml
resource_management:
  quota:
    requests:
      cpu: "4"     # Default: 4 CPU
      memory: 8Gi   # Default: 8Gi
  storage:
    enable_custom_storageclass: false
```

Teams can override the quota when they need more request capacity. The quota is shared by monitoring and other workloads in the namespace. It does not cap actual memory use when containers have no memory limit.

## In-depth description of parameters

| Parameter | Description | Example | Type | Default |
|---|---|---|---|---|
| `resource_management.quota.requests.cpu` | Maximum combined CPU requests for the team namespace. Use Kubernetes CPU quantities such as `500m` or `2`. | `"5"` | String | `"4"` |
| `resource_management.quota.requests.memory` | Maximum combined memory requests for the team namespace. Use Kubernetes memory quantities such as `512Mi` or `8Gi`. | `10Gi` | String | `8Gi` |
| `resource_management.storage.enable_custom_storageclass` | Allow the team namespace to use the additional storage classes listed in the chart. | `true` | Boolean | `false` |
