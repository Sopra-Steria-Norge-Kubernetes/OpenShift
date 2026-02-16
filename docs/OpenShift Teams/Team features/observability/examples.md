# Observability Examples

This page shows working configurations for common observability setups. See the parameter reference in the main page for details.

## Table of contents
- [Minimal (Grafana + Prometheus, small, no HA)](#minimal)
- [With Alertmanager and routes exposed](#am-with-routes)
- [HA Prometheus (medium) with Alertmanager](#ha-medium-am)
- [Grafana only (no Prometheus stack)](#grafana-only)
- [Prometheus only (no Grafana)](#prometheus-only)
- [Enable Alertmanager](#enable-alertmanager)
- [Basic Slack routing (single channel)](#slack-basic)
- [Severity-based routing (different channels)](#severity-routing)
- [Inhibition rules (mute lower severities if critical exists)](#inhibition-rules)

## Minimal (Grafana + Prometheus, small, no HA) {#minimal}

```yaml
observability:
  grafana_instance: true
  monitoringStack:
    enable: true
    monitoringStack_size: small
    monitoringStack_retention: 7d
    monitoringStack_high_availability: false
    monitoringStack_alertmanager: false
    monitoringStack_expose_route: false

rbac:
  team_monitoring_edit: <AD group for Grafana admin>
  team_monitoring_view: ""  # Optional
```

## Defining external prometheus datasaources

```yaml
observability:
  grafana_instance: true
  externalIPs_grafana:
    - 0.0.0.0/0              ## Prometheus datasource IP
  externalURLs_grafana:
    - my_datasource.com     ## Prometheus datasource URL
  monitoringStack:
    enable: true
    monitoringStack_size: small
    monitoringStack_retention: 7d
    monitoringStack_high_availability: false
    monitoringStack_alertmanager: false
    monitoringStack_expose_route: false
```

## With Alertmanager and routes exposed {#am-with-routes}

```yaml
observability:
  grafana_instance: true
  monitoringStack:
    enable: true
    monitoringStack_size: small
    monitoringStack_retention: 7d
    monitoringStack_high_availability: false
    monitoringStack_alertmanager: true
    monitoringStack_expose_route: true

rbac:
  team_monitoring_edit: <AD group for Grafana admin>
  team_monitoring_view: <AD group for Grafana view>
```

Note: When routes are exposed, anyone with network access to OpenShift can reach Prometheus/Thanos endpoints.

## HA Prometheus (medium) with Alertmanager {#ha-medium-am}

```yaml
observability:
  grafana_instance: true
  monitoringStack:
    enable: true
    monitoringStack_size: medium
    monitoringStack_retention: 7d
    monitoringStack_high_availability: true   # 2× Prometheus
    monitoringStack_alertmanager: true
    monitoringStack_expose_route: false
```

## Grafana only (no Prometheus stack) {#grafana-only}

```yaml
observability:
  grafana_instance: true
  monitoringStack:
    enable: false

rbac:
  team_monitoring_edit: <AD group for Grafana admin>
```

## Prometheus only (no Grafana) {#prometheus-only}

```yaml
observability:
  grafana_instance: false
  monitoringStack:
    enable: true
    monitoringStack_size: small
    monitoringStack_retention: 7d
```

## Enable Alertmanager {#enable-alertmanager}

```yaml
# values.yaml
observability:
  grafana_instance: true
  monitoringStack:
    enable: true
    monitoringStack_size: small
    monitoringStack_retention: 7d
    monitoringStack_high_availability: false
    monitoringStack_alertmanager: true
    monitoringStack_expose_route: false

rbac:
  team_monitoring_edit: <AD group for Grafana admin>
  team_monitoring_view: ""  # Optional
```

## Basic Slack routing (single channel) {#slack-basic}

1) Create a Secret with your Slack webhook:
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: alertmanager-slack-webhook
type: Opaque
stringData:
  webhook-url: https://hooks.slack.com/services/T00000000/B00000000/XXXXXXXXXXXXXXXXXXXXXXXX
```

Alternative: manage the webhook with External Secrets (recommended):
```yaml
apiVersion: external-secrets.io/v1beta1
kind: ExternalSecret
metadata:
  name: alertmanager-slack-webhook
spec:
  refreshInterval: 1h
  secretStoreRef:
    kind: ClusterSecretStore
    name: <your-secretstore-name>  # e.g. team-poseidon-secrets
  target:
    name: alertmanager-slack-webhook       # same Secret name AlertmanagerConfig references
    creationPolicy: Owner
    template:
      type: Opaque
  data:
  - secretKey: webhook-url                 # key in the generated k8s Secret
    remoteRef:
      key: <remote-secret-name>            # secret name in your external store (e.g. Azure Key Vault)
      # property: value                    # optional if your secret is a JSON object and you need a specific property
      # version: <version>                 # optional
```

2) Apply an AlertmanagerConfig:
```yaml
apiVersion: monitoring.coreos.com/v1alpha1
kind: AlertmanagerConfig
metadata:
  name: team-alertmanager-config
  labels:
    alertmanagerConfig: "true"
spec:
  route:
    receiver: slack-default
    groupBy: ["alertname", "namespace"]
    groupWait: 30s
    groupInterval: 5m
    repeatInterval: 3h
  receivers:
  - name: slack-default
    slackConfigs:
    - apiURL:
        name: alertmanager-slack-webhook
        key: webhook-url
      channel: "#alerts-team"
      sendResolved: true
      title: "[{{ .CommonLabels.cluster }}] {{ .CommonLabels.alertname }}"
      text: |
        Severity: {{ .CommonLabels.severity }}
        Namespace: {{ .CommonLabels.namespace }}
        {{ range .Alerts -}}
        Summary: {{ .Annotations.summary }}
        Description: {{ .Annotations.description }}
        {{ end }}
```

## Severity-based routing (different channels) {#severity-routing}

Use different receivers for critical vs. warning/info.

```yaml
apiVersion: monitoring.coreos.com/v1alpha1
kind: AlertmanagerConfig
metadata:
  name: team-alertmanager-config
  labels:
    alertmanagerConfig: "true"
spec:
  route:
    receiver: slack-general
    groupBy: ["alertname", "namespace"]
    routes:
    - matchers:
      - name: severity
        value: critical
        matchType: =
      receiver: slack-oncall
  receivers:
  - name: slack-oncall
    slackConfigs:
    - apiURL:
        name: alertmanager-slack-webhook
        key: webhook-url
      channel: "#alerts-oncall"
      sendResolved: true
      title: "[CRITICAL] {{ .CommonLabels.alertname }}"
      text: "Namespace: {{ .CommonLabels.namespace }} | {{ .CommonAnnotations.summary }}"
  - name: slack-general
    slackConfigs:
    - apiURL:
        name: alertmanager-slack-webhook
        key: webhook-url
      channel: "#alerts-general"
      sendResolved: true
      title: "[{{ .CommonLabels.severity | toUpper }}] {{ .CommonLabels.alertname }}"
      text: "Namespace: {{ .CommonLabels.namespace }} | {{ .CommonAnnotations.summary }}"
```

## Inhibition rules (mute lower severities if critical exists) {#inhibition-rules}

Prevent duplicate noise when a higher severity alert is active for the same target.

```yaml
apiVersion: monitoring.coreos.com/v1alpha1
kind: AlertmanagerConfig
metadata:
  name: team-alertmanager-config
  labels:
    alertmanagerConfig: "true"
spec:
  route:
    receiver: slack-general
  receivers:
  - name: slack-general
    slackConfigs:
    - apiURL:
        name: alertmanager-slack-webhook
        key: webhook-url
      channel: "#alerts-general"
      sendResolved: true
  inhibitRules:
  - sourceMatch:
    - name: severity
      value: critical
    targetMatch:
    - name: severity
      value: warning
    equal: ["alertname", "namespace"]
  - sourceMatch:
    - name: severity
      value: critical
    targetMatch:
    - name: severity
      value: info
    equal: ["alertname", "namespace"]
```

Notes:
- Alertmanager runs with 2 replicas by default when enabled.
- Replace Slack channels and webhook Secret names as appropriate.
- You can store the webhook in an external secret manager and sync it into the Secret shown above if preferred.
