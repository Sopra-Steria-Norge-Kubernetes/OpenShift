# Observability Feature

With Observability you can:

- Run a team-hosted Grafana that uses OpenShift login
- Auto-provision datasources for all tenants managed by the team
- Deploy a team Prometheus stack (Prometheus + Thanos sidecar, optional Alertmanager)
- Build dashboards and set up alerting for your workloads
- Scrape application metrics using ServiceMonitor/PodMonitor
- Optionally expose routes for Prometheus and Thanos Query for easy access

## Minimal configuration (Grafana + Prometheus, small, no HA)
See full examples here: [Observability Examples](./examples.md)

## Parameters

### Observability
| Parameter | Type | Default | Description | Link |
|---|---|---|---|---|
| observability.grafana_instance | Boolean | false | Enable Grafana with OpenShift auth | [Details](#param-observability-grafana_instance) |

### RBAC
| Parameter | Type | Default | Description | Link |
|---|---|---|---|---|
| rbac.team_monitoring_edit | String | "" | AD group with admin access to Grafana | [Details](#param-rbac-team_monitoring_edit) |
| rbac.team_monitoring_view | String | "" | AD group with viewer access (optional) | [Details](#param-rbac-team_monitoring_view) |

### observability.monitoringStack
| Parameter | Type | Default | Description | Link |
|---|---|---|---|---|
| observability.monitoringStack.enable | Boolean | false | Enable Prometheus stack | [Details](#param-observability-monitoringstack-enable) |
| observability.monitoringStack.monitoringStack_size | String | small | Size preset (small/medium/large) | [Details](#param-observability-monitoringstack-size) |
| observability.monitoringStack.monitoringStack_high_availability | Boolean | false | Run 2 Prometheus replicas | [Details](#param-observability-monitoringstack-ha) |
| observability.monitoringStack.monitoringStack_retention | String | 7d | Metrics retention window | [Details](#param-observability-monitoringstack-retention) |
| observability.monitoringStack.monitoringStack_alertmanager | Boolean | false | Enable Alertmanager (2 replicas) | [Details](#param-observability-monitoringstack-alertmanager) |
| observability.monitoringStack.monitoringStack_expose_route | Boolean | false | Expose Prometheus/Thanos routes | [Details](#param-observability-monitoringstack-expose-route) |

## Parameter details

### Observability
#### observability.grafana_instance {#param-observability-grafana_instance}
Enables a team Grafana instance using OpenShift OAuth. Automatically wires datasources for team-managed tenants.
- Requires RBAC groups (see below).
- Route format: `https://<team-name>-grafana.<cluster-apps-domain>`

### RBAC
#### rbac.team_monitoring_edit {#param-rbac-team_monitoring_edit}
AD group with admin rights in Grafana. Mandatory when enabling Grafana.

#### rbac.team_monitoring_view {#param-rbac-team_monitoring_view}
AD group with view-only rights. Optional.

### observability.monitoringStack
#### observability.monitoringStack.enable {#param-observability-monitoringstack-enable}
Enables the team monitoring stack (Prometheus + Thanos sidecar; Alertmanager optional).

#### observability.monitoringStack.monitoringStack_size {#param-observability-monitoringstack-size}
Controls base requests for components (Prometheus, Grafana, Thanos sidecar; Alertmanager if enabled).
- Options: small, medium, large
- See sizing page for totals and math: [Monitoring Stack Sizing](./monitoring-stack-sizing.md)

#### observability.monitoringStack.monitoringStack_high_availability {#param-observability-monitoringstack-ha}
Runs 2 Prometheus replicas for resiliency.
- Prometheus PVC: 10Gi per replica (total 20Gi with HA)

#### observability.monitoringStack.monitoringStack_retention {#param-observability-monitoringstack-retention}
Sets metrics retention window (e.g., 3d, 7d, 30d, 24h).
- Fit retention to allocated storage (10Gi per Prometheus replica)
- Reduce retention if nearing capacity

#### observability.monitoringStack.monitoringStack_alertmanager {#param-observability-monitoringstack-alertmanager}
Enables Alertmanager (assumed 2 replicas by default).
- Required for alert routing/notifications
- Size-derived resource requests; see [Monitoring Stack Sizing](./monitoring-stack-sizing.md)

#### observability.monitoringStack.monitoringStack_expose_route {#param-observability-monitoringstack-expose-route}
Creates routes for Prometheus and Thanos Query.
- Prometheus: `https://prometheus-<team-name>.<cluster-apps-domain>`
- Thanos Query: `https://thanos-query-<team-name>.<cluster-apps-domain>`

!!! warning
    When routes are exposed, anyone with network access to the OpenShift environment can access Prometheus and Thanos endpoints.

## Operational notes

- Storage: Prometheus uses a fixed 10Gi PVC per replica (cannot be increased in-place).
- If approaching storage limits, reduce `monitoringStack_retention` and contact Sopra Steria for storage expansion.
- Datasources in Grafana are provisioned automatically for team tenants.

## Discovering endpoints

Using OC:
```bash
oc get route <team-name>-grafana-route -n <team-namespace>
oc get routes -n <team-namespace>  # Prometheus/Thanos if expose_route=true
```

## Monitoring your applications

Use ServiceMonitor or PodMonitor to scrape application metrics:
- ServiceMonitor: scrape via Services (LB across replicas)
- PodMonitor: scrape pods directly (DaemonSet/StatefulSet-friendly)

Examples:
- [ServiceMonitor Examples](./servicemonitor-examples.md)
- [PodMonitor Examples](./podmonitor-examples.md)

### Getting Started

1. **Choose your stack size** based on your workload
2. **Set retention period** appropriate for your needs
3. **Enable high availability** for production environments
4. **Enable alertmanager** if you need notifications
5. **Deploy your configuration** and access Grafana through your team's route
6. **Access your services**:
   - **Grafana**: `https://<team-name>-grafana.<cluster-apps-domain>`
   - **Prometheus** (if route enabled): `https://prometheus-<team-name>.<cluster-apps-domain>`
   - **Thanos Query** (if route enabled): `https://thanos-query-<team-name>.<cluster-apps-domain>`

Your monitoring stack will be automatically configured with proper labels and selectors to monitor resources in your team namespace.

## Monitoring Your Applications

To monitor your applications with the team monitoring stack, you need to create monitoring resources that tell Prometheus how to discover and scrape metrics from your applications.

### ServiceMonitor vs PodMonitor

- **ServiceMonitor**: Monitors applications through Kubernetes services. Use this when you have services in front of your pods and want load balancing across replicas.
- **PodMonitor**: Monitors pods directly. Use this for more granular control, when monitoring DaemonSets/StatefulSets, or when you don't have services.

For detailed configuration examples, see:
- [ServiceMonitor Examples](./servicemonitor-examples.md) - Basic and authenticated service monitoring
- [PodMonitor Examples](./podmonitor-examples.md) - Direct pod monitoring and StatefulSet monitoring

### Service Discovery

All deployed observability services can be found in your team namespace:

```bash
# List all services
oc get svc -n <team-namespace>

# List all routes (external access)
oc get routes -n <team-namespace>

# Check specific observability pods
oc get pods -l app.kubernetes.io/part-of=observability -n <team-namespace>
```
- **ServiceMonitor**: Monitors applications through Kubernetes services. Use this when you have services in front of your pods and want load balancing across replicas.
- **PodMonitor**: Monitors pods directly. Use this for more granular control, when monitoring DaemonSets/StatefulSets, or when you don't have services.

For detailed configuration examples, see:
- [ServiceMonitor Examples](./servicemonitor-examples.md) - Basic and authenticated service monitoring
- [PodMonitor Examples](./podmonitor-examples.md) - Direct pod monitoring and StatefulSet monitoring

### Service Discovery

All deployed observability services can be found in your team namespace:

```bash
# List all services
oc get svc -n <team-namespace>

# List all routes (external access)
oc get routes -n <team-namespace>

# Check specific observability pods
oc get pods -l app.kubernetes.io/part-of=observability -n <team-namespace>
```

All deployed observability services can be found in your team namespace:

```bash
# List all services
oc get svc -n <team-namespace>

# List all routes (external access)
oc get routes -n <team-namespace>

# Check specific observability pods
oc get pods -l app.kubernetes.io/part-of=observability -n <team-namespace>
```

