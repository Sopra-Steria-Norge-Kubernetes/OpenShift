# ServiceMonitor Examples

ServiceMonitor resources define how Prometheus discovers and scrapes metrics from Kubernetes services.

These examples target the Cluster Observability Operator MonitoringStack API.


**All ServiceMonitor resources MUST include the team label `soprasteria/team: "<team-name>"` or they will be ignored by the team Prometheus stack.**

The team monitoring stack uses a `resourceSelector` that only discovers monitoring resources with this specific label. This ensures teams can only monitor resources in their own scope.

Note: Target Services do not require the team label. ServiceMonitor resources do.

## Prerequisites

- Team monitoring stack must be enabled (`observability.monitoringStack.enable: true`)
- Your application must expose metrics on an HTTP endpoint
- ServiceMonitors must have the team label: `soprasteria/team: "<team-name>"`

### Application Metrics Requirements

Your application must expose Prometheus-compatible metrics on an HTTP endpoint. Common requirements:

1. **Metrics Endpoint**: Usually `/metrics` (configurable in ServiceMonitor)
2. **HTTP Port**: Accessible port that Prometheus can scrape
3. **Metrics Format**: Prometheus text format or OpenMetrics format
4. **Content-Type**: Should return `text/plain` or `application/openmetrics-text`

**Example metrics endpoint response:**
```
# HELP http_requests_total Total number of HTTP requests
# TYPE http_requests_total counter
http_requests_total{method="GET",status="200"} 1027
http_requests_total{method="POST",status="200"} 3

# HELP http_request_duration_seconds HTTP request duration in seconds
# TYPE http_request_duration_seconds histogram
http_request_duration_seconds_bucket{le="0.1"} 100
http_request_duration_seconds_bucket{le="+Inf"} 1030
http_request_duration_seconds_sum 25.5
http_request_duration_seconds_count 1030
```

## Example 1: Basic ServiceMonitor

This example monitors a simple web application exposing metrics on `/metrics`.
The label chain is:

- `ServiceMonitor.spec.selector.matchLabels.app` -> matches `Service.metadata.labels.app`
- `Service.spec.selector.app` -> selects Pods with the same `app` label

```yaml
apiVersion: monitoring.rhobs/v1
kind: ServiceMonitor
metadata:
  name: my-app-servicemonitor
  namespace: <application namespace>
  labels:
    soprasteria/team: "<team-name>"  # Required for team monitoring stack
spec:
  selector:
    matchLabels:
      app: my-web-app
  endpoints:
  - port: metrics
    path: /metrics
    interval: 30s
```

**Corresponding Service:**
```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-web-app-service
  namespace: <application namespace>
  labels:
    app: my-web-app  # Matched by ServiceMonitor spec.selector.matchLabels
spec:
  selector:
    app: my-web-app  # Selects Pods with label app=my-web-app
  ports:
  - name: metrics  # This name is referenced in ServiceMonitor
    port: 8080
    targetPort: 8080
    protocol: TCP
```

## Troubleshooting

### Common Issues

1. **Missing team label**: Ensure the ServiceMonitor has `soprasteria/team: "<team-name>"`
2. **Label mismatch**: Ensure ServiceMonitor `matchLabels` matches Service `metadata.labels`
3. **Port name mismatch**: Ensure ServiceMonitor endpoint port matches Service port name
4. **Metrics endpoint**: Verify your application exposes metrics on the specified path

### Validation Commands

```bash
# Check ServiceMonitor exists
oc get servicemonitors.monitoring.rhobs -n <application namespace>

# Verify ServiceMonitor selector labels
oc get servicemonitors.monitoring.rhobs my-app-servicemonitor -n <application namespace> -o yaml

# Verify Service labels
oc get svc my-web-app-service -n <application namespace> -o yaml

# Test metrics endpoint directly
oc exec -it <pod-name> -n <application namespace> -- curl localhost:8080/metrics
```
