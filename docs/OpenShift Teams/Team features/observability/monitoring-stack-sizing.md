# Monitoring Stack Resources

The team chart uses per-component resource settings instead of `small`, `medium`, or `large` presets. Teams can override requests and limits independently.

## Default Resources

| Component | Default request per replica | Replicas | Default limit |
|---|---|---|---|
| Prometheus | 500m CPU, 1Gi memory | 1; 2 with HA | None |
| Thanos sidecar | 100m CPU, 256Mi memory | One per Prometheus replica | None |
| Grafana | 200m CPU, 512Mi memory | 1 when enabled | None |
| Alertmanager | 100m CPU, 256Mi memory | 1 when enabled; 2 with HA | None |
| ThanosQuerier | LimitRange default request: 10m CPU, 64Mi memory | 1 | None |

The LimitRange supplies default requests only. It does not supply limits. ThanosQuerier's operator API does not expose a resource or replica setting in this chart.

## Configure Resources

Set Prometheus, Thanos sidecar, and Alertmanager under `observability.monitoringStack.resources`. Set Grafana under `observability.grafana_resources`. Each component accepts Kubernetes `requests` and `limits` values.

```yaml
observability:
	monitoringStack:
		enable: true
		monitoringStack_high_availability: false
		resources:
			thanosSidecar:
				requests:
					cpu: 150m
					memory: 320Mi
				limits: null
```

Components omitted from the values use the defaults in the table. The old `monitoringStack_size` setting is no longer used and should be removed from team values.

When HA is enabled, the chart runs two Prometheus replicas and therefore two Thanos sidecars. If Alertmanager is enabled, it also runs two replicas. ThanosQuerier remains a single replica.

## Namespace Quota

The team namespace quota defaults to `4` CPU and `8Gi` for `requests.cpu` and `requests.memory`. Teams can override it:

```yaml
resource_management:
	quota:
		requests:
			cpu: "5"
			memory: 10Gi
```

The quota is shared by the monitoring stack and all other workloads in the team namespace. It is a limit on aggregate resource requests, not a reservation for monitoring. Because component limits default to none, the requests quota does not cap actual memory use.

Return to: [Observability](./observability.md)
