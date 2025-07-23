# Observability Feature

With observability you can enable a team grafana instance that uses the openshift credentials for login. This grafana instance will automatically have datasources from all tenants managed by the team unless specifically disabled in the tenant definitions values yaml.

The grafana instance must have an admin group. it does not require editor group. but can be specified if multiple access levels is wanted.

You can also enable a dedicated monitoring stack with Prometheus for your team namespace. datasource for this will be automatically configured in the grafana instance.

## Grafana Configuration

Configure Grafana access using the team's RBAC settings:

```yaml
observability:
  grafana_instance: true

rbac:
  team_monitoring_edit: <AD Group for Grafana admin access>
  team_monitoring_view: <AD Group for Grafana view access>
```

### Access Levels

- **Admin Access**: Users in the `team_monitoring_edit` group will have full administrative access to Grafana
- **View Access**: Users in the `team_monitoring_view` group will have read-only access to Grafana dashboards
- **Default Access**: If `team_monitoring_view` is empty, all authenticated users will have view access

### Accessing Monitoring Services

Once your monitoring stack is deployed, you can access find the urls for your monitoring services by typing the following command:

!!! example "Find Your Monitoring URLs"
    ```bash
    # List all routes in your team namespace
    oc get routes -n <team-namespace>
    ```

    Your monitoring services will be available at these URLs:

    - **Grafana**: `https://<team-name>-grafana.<cluster-apps-domain>`
    - **Prometheus** (if routes enabled): `https://prometheus-<team-name>.<cluster-apps-domain>`
    - **Thanos Query** (if routes enabled): `https://thanos-query-<team-name>.<cluster-apps-domain>`

    Replace `<team-name>` with your actual team name and `<cluster-apps-domain>` with your cluster's application domain.


## Team Monitoring Stack

The team monitoring stack provides Prometheus-based monitoring for your OpenShift team namespace.

### Quick Start

To enable monitoring for your team, configure the following in your `values.yaml`:

```yaml
observability:
  grafana_instance: true
  monitoringStack:
    enable: true
    monitoringStack_size: small
    monitoringStack_retention: 7d
```

### Configuration Options

#### Enable Monitoring Stack
- **Parameter**: `monitoringStack.enable`
- **Type**: Boolean
- **Default**: `false`
- **Description**: Enables the Prometheus monitoring stack for your team

#### Stack Size

Choose the appropriate size based on your monitoring needs:

| Size | CPU | Memory | Recommended For |
|------|-----|---------|-----------------|
| `small` | 300m | 300Mi | Development, small workloads |
| `medium` | 500m | 500Mi | Production, moderate workloads |
| `large` | 1000m | 1Gi | High-traffic production |

- **Parameter**: `monitoringStack.monitoringStack_size`
- **Options**: `small`, `medium`, `large`
- **Default**: `small`

#### High Availability
- **Parameter**: `monitoringStack.monitoringStack_high_availability`
- **Type**: Boolean
- **Default**: `false`
- **Description**: Runs 2 Prometheus replicas for better reliability

When enabled, the monitoring stack will deploy 2 Prometheus replicas instead of 1. This provides:
- **Redundancy**: If one replica fails, the other continues collecting metrics
- **Load Distribution**: Metrics collection is distributed across both replicas
- **Zero Downtime**: Updates and maintenance can be performed without service interruption
- **Improved Reliability**: Reduced risk of monitoring gaps during pod restarts or node failures

!!! info "Storage Information"
    The monitoring stack uses 10GB of persistent storage per Prometheus replica. This storage size cannot be increased by changing the configuration due to StatefulSet limitations with PVC resizing. Plan your retention period accordingly to fit within this storage limit.

    If you are approaching the storage limit, reduce the retention period and contact Sopra Steria for manual storage expansion.

#### Data Retention
- **Parameter**: `monitoringStack.monitoringStack_retention`
- **Type**: String
- **Default**: `7d`
- **Description**: How long to keep metrics data

**Examples**: `3d`, `7d`, `30d`, `24h`

#### Alertmanager
- **Parameter**: `monitoringStack.monitoringStack_alertmanager`
- **Type**: Boolean
- **Default**: `false`
- **Description**: Enables alert notifications and routing

#### Expose Routes
- **Parameter**: `monitoringStack.monitoringStack_expose_route`
- **Type**: Boolean
- **Default**: `false`
- **Description**: Exposes Prometheus and Thanos Query interfaces via routes

When enabled, the monitoring stack components will be accessible via routes:

**Prometheus**: `https://prometheus-<team-name>.<cluster-apps-domain>`
**Thanos Query**: `https://thanos-query-<team-name>.<cluster-apps-domain>`

!!! warning "Security Warning"
    These routes are publicly accessible without authentication when exposed for anyone who has network access to the OpenShift environment.

#### Data Retention Policy
- **Parameter**: `monitoringStack.monitoringStack_retain_data`
- **Type**: Boolean
- **Default**: `false`
- **Description**: Controls whether monitoring data persists when the monitoring stack is deleted

- When `false`: All monitoring data is deleted when the stack is removed (ArgoCD sync option: `Delete=true`)
- When `true`: Monitoring data persists even if the stack is deleted

#### Storage Configuration
- **Parameter**: `monitoringStack.monitoringStack_persistent_storage`
- **Type**: String
- **Default**: `10Gi`
- **Description**: Storage size per Prometheus replica

!!! warning "Storage Limitations"
    Due to StatefulSet limitations with PVC resizing, the storage size cannot be increased after deployment. Plan your storage needs carefully based on your retention period and expected metrics volume.

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

## Post-Deployment Operations

### Verifying Your Monitoring Stack

After deploying your configuration, verify the monitoring stack is running:

!!! example "Verification Commands"
    ```bash
    # Check monitoring stack pods
    oc get pods -n <team-namespace> | grep -E "(prometheus|grafana|thanos)"

    # Check monitoring stack services
    oc get svc -n <team-namespace> | grep -E "(prometheus|grafana|thanos)"

    # Check MonitoringStack resource
    oc get monitoringstack -n <team-namespace>

    # Check monitoring stack status
    oc describe monitoringstack <team-name>-monitoringstack -n <team-namespace>
    ```

### Resource Usage Monitoring

Monitor your monitoring stack's resource usage:

```bash
# Check Prometheus storage usage
oc exec -it prometheus-<team-name>-0 -n <team-namespace> -- df -h /prometheus

# Check memory and CPU usage
oc top pods -n <team-namespace> | grep prometheus

# Check PVC usage
oc get pvc -n <team-namespace>
```

### Troubleshooting Common Issues

**Common Problems and Solutions:**
1. **Grafana not accessible**: Check route exists and RBAC groups are configured
2. **No metrics appearing**: Verify ServiceMonitor/PodMonitor resources have team labels
3. **Storage full**: Reduce retention period or contact platform team for storage expansion
4. **High resource usage**: Consider upgrading stack size or reducing scrape frequency

## Monitoring Your Applications

To monitor your applications with the team monitoring stack, you need to create monitoring resources that tell Prometheus how to discover and scrape metrics from your applications.

### Quick Start for Developers

If your team admin has already enabled the monitoring stack, you can start monitoring your applications in 3 steps:

1. **Ensure your application exposes metrics** on an HTTP endpoint (e.g., `/metrics`)
2. **Create a ServiceMonitor or PodMonitor** resource with the team label
3. **Verify metrics collection** in Grafana

**Minimal ServiceMonitor example:**
```yaml
apiVersion: monitoring.coreos.com/v1
kind: ServiceMonitor
metadata:
  name: my-app-monitor
  namespace: <team-namespace>
  labels:
    soprasteria/team: "<team-name>"  # CRITICAL: Required for discovery
spec:
  selector:
    matchLabels:
      app: my-app  # Must match your service labels
  endpoints:
  - port: metrics    # Must match your service port name
    path: /metrics   # Your metrics endpoint path
    interval: 30s
```

### ServiceMonitor vs PodMonitor

- **ServiceMonitor**: Monitors applications through Kubernetes services. Use this when you have services in front of your pods and want load balancing across replicas.
- **PodMonitor**: Monitors pods directly. Use this for more granular control, when monitoring DaemonSets/StatefulSets, or when you don't have services.

For detailed configuration examples, see:
- [ServiceMonitor Examples](servicemonitor-examples.md) - Basic and authenticated service monitoring
- [PodMonitor Examples](podmonitor-examples.md) - Direct pod monitoring and StatefulSet monitoring

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

## In-depth description of parameters

| <div style="width:140px">**Variable**</div>         | **Description**                                                                                                     | **Example**                                | **Type**                  | **Default Value**  |
|----------------------|---------------------------------------------------------------------------------------------------------------------|--------------------------------------------|---------------------------|------------|
| **Grafana Configuration**              |                                                                                                                     |                                            |                           |
| `grafana_instance`            | Enable grafana instance in team namespace                               | true / false  | Boolean                    | false |
| **RBAC Configuration**              |                                                                                                                     |                                            |                           |
| `rbac.team_monitoring_edit`            | AD Group for Grafana admin access                               | my-admin-group  | String                    | "" |
| `rbac.team_monitoring_view`            | AD Group for Grafana view access                                    | my-view-group | String                    | "" |
| **Monitoring Stack Configuration**              |                                                                                                                     |                                            |                           |
| `monitoringStack.enable`            | Enable Prometheus monitoring stack for team namespace                               | true / false  | Boolean                    | false |
| `monitoringStack.monitoringStack_size`            | Size of the monitoring stack resources                               | small / medium / large  | String                    | small |
| `monitoringStack.monitoringStack_high_availability`            | Run 2 Prometheus replicas for better reliability                                    | true / false | Boolean                    | false |
| `monitoringStack.monitoringStack_retention`            | How long to keep metrics data                                    | 7d / 30d / 24h | String                    | 7d |
| `monitoringStack.monitoringStack_alertmanager`            | Enable alert notifications and routing                                    | true / false | Boolean                    | false |
| `monitoringStack.monitoringStack_expose_route`            | Expose Prometheus and Thanos Query interfaces via routes                                    | true / false | Boolean                    | false |
| `monitoringStack.monitoringStack_retain_data`            | Keep monitoring data when stack is deleted                                    | true / false | Boolean                    | false |
| `monitoringStack.monitoringStack_persistent_storage`            | Storage size per Prometheus replica                                    | 10Gi / 20Gi | String                    | 10Gi |
