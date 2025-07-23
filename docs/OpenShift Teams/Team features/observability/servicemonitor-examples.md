# ServiceMonitor Examples

ServiceMonitor resources define how Prometheus discovers and scrapes metrics from Kubernetes services.

⚠️ **CRITICAL: Team Label Required for Discovery**

**All ServiceMonitor resources MUST include the team label `soprasteria/team: "<your-team-name>"` or they will be completely ignored by the team Prometheus stack and no metrics will be collected.**

The team monitoring stack uses a `resourceSelector` that only discovers monitoring resources with this specific label. This is a security feature that ensures teams can only monitor resources within their own namespace.

Note: The target Services themselves do not require the team label - only the ServiceMonitor resources need it.

## Prerequisites

- Team monitoring stack must be enabled (`observability.monitoringStack.enable: true`)
- Your application must expose metrics on an HTTP endpoint
- **CRITICAL**: ServiceMonitors must have the team label: `soprasteria/team: "<team-name>"`

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

This example monitors a simple web application exposing metrics on `/metrics`:

```yaml
apiVersion: monitoring.coreos.com/v1
kind: ServiceMonitor
metadata:
  name: my-app-servicemonitor
  namespace: team-example
  labels:
    soprasteria/team: "team-example"  # Required for team monitoring stack
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
  namespace: team-example
  labels:
    app: my-web-app  # This label is used by ServiceMonitor selector
spec:
  selector:
    app: my-web-app
  ports:
  - name: metrics  # This name is referenced in ServiceMonitor
    port: 8080
    targetPort: 8080
    protocol: TCP
```

## Example 2: ServiceMonitor with Authentication

For applications requiring authentication to access metrics:

```yaml
apiVersion: monitoring.coreos.com/v1
kind: ServiceMonitor
metadata:
  name: authenticated-app-monitor
  namespace: team-example
  labels:
    soprasteria/team: "team-example"
spec:
  selector:
    matchLabels:
      app: secure-app
  endpoints:
  - port: metrics
    path: /metrics
    interval: 30s
    basicAuth:
      username:
        name: metrics-auth-secret
        key: username
      password:
        name: metrics-auth-secret
        key: password
```

**Required Secret:**
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: metrics-auth-secret
  namespace: team-example
type: Opaque
data:
  username: <base64-encoded-username>
  password: <base64-encoded-password>
```

## Troubleshooting

### Common Issues

1. **Missing team label**: Ensure the ServiceMonitor has `soprasteria/team: "<team-name>"` label
2. **Port name mismatch**: The port name in ServiceMonitor must match the service port name
3. **Metrics endpoint**: Verify your application exposes metrics on the specified path

### Validation Commands

```bash
# Check ServiceMonitor exists
oc get servicemonitor -n <team-namespace>

# Test metrics endpoint directly
oc exec -it <pod-name> -n <team-namespace> -- curl localhost:8080/metrics

# Check if Prometheus is scraping the target
oc port-forward svc/prometheus-operated -n <team-namespace> 9090:9090
# Then visit http://localhost:9090/targets
```
