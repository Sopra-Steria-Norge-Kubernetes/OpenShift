# Resource Management

Efficient allocation and utilization of CPU and memory resources in OpenShift.

**Official Documentation:** [Managing Resources](https://docs.openshift.com/container-platform/latest/applications/quotas/quotas-setting-per-project.html) 

## Resource Requests and Limits

Each container can specify CPU and memory requirements:

- **Resource Request**: Amount OpenShift reserves on the node. Used by scheduler for pod placement
- **Resource Limit**: Maximum amount a container can consume
  - Memory limit exceeded → pod terminated (OOMKilled)
  - CPU limit exceeded → process throttled (not terminated)

Properly defined requests and limits prevent resource contention and ensure fair allocation.

## Tenant Resource Quotas

Each tenant has a ClusterResourceQuota defining maximum CPU and memory that can be requested across all tenant namespaces.

### Request vs. Limit in Quotas

- **Request quota**: Hard cap on total requested resources across all pods in tenant
- **Limits**: Should be set at pod level, not quota level, for flexibility

### Best Practices

- Set requests based on actual resource needs under normal conditions
- Define limits at pod level to prevent overconsumption
- Monitor and adjust quotas regularly
- Requests don't restrict actual usage—pods can consume more if available

## Scaling Workloads

### Horizontal Pod Autoscaling (HPA)
Automatically adjusts number of pod replicas based on CPU utilization or other metrics.

### Vertical Pod Autoscaling (VPA)
Automatically adjusts CPU and memory requests based on actual usage. Restarts pods when necessary.

### HPA and VPA in OpenShift Tenants

**Not recommended** in environments with ClusterResourceQuotas due to:

- **Quota Exhaustion**: VPA may increase requests beyond quota limits
- **Unpredictable Scaling**: Difficult to plan quota allocation
- **Service Disruptions**: VPA requires pod restarts
- **Resource Starvation**: Uneven resource allocation

**Recommendation**: Manually define and regularly review resource requests/limits for predictable resource usage.

Because of these risks, we do not necessarily recommend using HPA or VPA in our environment, particularly where ClusterResourceQuotas are in place. Instead, resource requests and limits should be manually defined and regularly reviewed to ensure a predictable and balanced use of resources across tenants.

## Resource Monitoring and Optimization

- Track usage patterns and detect anomalies
- Fine-tune requests/limits based on actual behavior
- Prevent overprovisioning and quota exhaustion
- Maintain stable, predictable, and efficient environments