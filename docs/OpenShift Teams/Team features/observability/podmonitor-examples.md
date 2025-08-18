# PodMonitor Examples

PodMonitor resources allow Prometheus to directly scrape metrics from pods, bypassing Kubernetes services. This is useful for monitoring pods that don't have services or when you need more granular control.

⚠️ **CRITICAL: Team Label Required for Discovery**

**All PodMonitor resources MUST include the team label `soprasteria/team: "<your-team-name>"` or they will be completely ignored by the team Prometheus stack and no metrics will be collected.**

The team monitoring stack uses a `resourceSelector` that only discovers monitoring resources with this specific label. This is a security feature that ensures teams can only monitor resources within their own namespace.

Note: The target Pods themselves do not require the team label - only the PodMonitor resources need it.

## Prerequisites

- Team monitoring stack must be enabled (`observability.monitoringStack.enable: true`)
- Your application pods must expose metrics on an HTTP endpoint
- **CRITICAL**: PodMonitors must have the team label: `soprasteria/team: "<team-name>"`

### Application Metrics Requirements

Your application must expose Prometheus-compatible metrics on an HTTP endpoint. Common requirements:

1. **Metrics Endpoint**: Usually `/metrics` (configurable in PodMonitor)
2. **HTTP Port**: Accessible port that Prometheus can scrape
3. **Metrics Format**: Prometheus text format or OpenMetrics format
4. **Content-Type**: Should return `text/plain` or `application/openmetrics-text`

**Testing your metrics endpoint:**
```bash
# Test from within your pod
oc exec -it <pod-name> -n <team-namespace> -- curl localhost:8080/metrics

# Test from another pod in the same namespace
oc run test-pod --image=curlimages/curl -it --rm -- curl <pod-ip>:8080/metrics
```

## Example 1: Basic PodMonitor

This example monitors pods directly by selecting them with labels:

```yaml
apiVersion: monitoring.coreos.com/v1
kind: PodMonitor
metadata:
  name: my-app-podmonitor
  namespace: team-example
  labels:
    soprasteria/team: "team-example"  # Required for team monitoring stack
spec:
  selector:
    matchLabels:
      app: my-web-app
  podMetricsEndpoints:
  - port: metrics
    path: /metrics
    interval: 30s
```

**Corresponding Deployment:**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-web-app
  namespace: team-example
spec:
  replicas: 2
  selector:
    matchLabels:
      app: my-web-app
  template:
    metadata:
      labels:
        app: my-web-app
        soprasteria/team: "team-example"  # Optional - not required for monitoring discovery
    spec:
      containers:
      - name: web-app
        image: my-web-app:latest
        ports:
        - name: metrics  # This name is referenced in PodMonitor
          containerPort: 8080
```

## Example 2: PodMonitor for StatefulSet

Monitor StatefulSet pods with consistent naming and additional metadata:

```yaml
apiVersion: monitoring.coreos.com/v1
kind: PodMonitor
metadata:
  name: database-podmonitor
  namespace: team-example
  labels:
    soprasteria/team: "team-example"
spec:
  selector:
    matchLabels:
      app: postgresql
      statefulset: postgres-cluster
  podMetricsEndpoints:
  - port: metrics
    path: /metrics
    interval: 60s
    relabelings:
    - sourceLabels: [__meta_kubernetes_pod_name]
      targetLabel: pod_name
    - sourceLabels: [__meta_kubernetes_pod_node_name]
      targetLabel: node_name
```

**Corresponding StatefulSet:**
```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: postgres-cluster
  namespace: team-example
spec:
  serviceName: postgres
  replicas: 3
  selector:
    matchLabels:
      app: postgresql
      statefulset: postgres-cluster
  template:
    metadata:
      labels:
        app: postgresql
        statefulset: postgres-cluster
        soprasteria/team: "team-example"  # Optional - not required for monitoring discovery
    spec:
      containers:
      - name: postgres
        image: postgres:13
        ports:
        - name: metrics
          containerPort: 9187
```

## PodMonitor vs ServiceMonitor

### Use PodMonitor when:
- You need to monitor pods without services
- You want more granular control over which pods to monitor
- You're monitoring DaemonSets or StatefulSets directly
- You need pod-specific metadata in metrics

### Use ServiceMonitor when:
- You have services in front of your pods
- You want load balancing across multiple pod replicas
- You prefer service-based discovery

## Troubleshooting

### Common Issues

1. **Missing team label**: Ensure the PodMonitor has `soprasteria/team: "<team-name>"` label
2. **Port name mismatch**: The port name in PodMonitor must match the container port name
3. **Pod selector**: Ensure the PodMonitor selector matches your pod labels
4. **Metrics endpoint**: Verify your application exposes metrics on the specified path

### Validation Commands

```bash
# Check PodMonitor exists
oc get podmonitor -n <team-namespace>

# List pods that should be monitored
oc get pods -l app=<app-label> -n <team-namespace>

# Test metrics endpoint directly from pod
oc exec -it <pod-name> -n <team-namespace> -- curl localhost:8080/metrics

# Check if Prometheus is scraping the targets
oc port-forward svc/prometheus-operated -n <team-namespace> 9090:9090
# Then visit http://localhost:9090/targets
```
