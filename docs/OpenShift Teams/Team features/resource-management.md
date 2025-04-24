# Resource Management

Resource managment is used to set the cpu and memory request quotas for the team namespace.

```yaml
resource_management:
  requests:
    cpu: "200m"
    memory: "500Mi"
  storage:
    enable_custom_storageclass: false
```