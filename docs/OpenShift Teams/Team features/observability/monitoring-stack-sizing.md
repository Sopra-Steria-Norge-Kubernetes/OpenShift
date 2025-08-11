# Monitoring Stack Sizing

This page details the estimated resource reservations for the team monitoring stack components (Prometheus, Grafana, Thanos sidecar, Alertmanager) across supported sizes and deployment modes (with / without HA, with / without Alertmanager).

> These numbers reflect container **requests** (not limits). Always verify against your deployed resources, as platform overrides or chart updates may change defaults.

## Component Baseline (Per Size)

| Component / Size | small (CPU / Mem) | medium (CPU / Mem) | large (CPU / Mem) |
|------------------|------------------|--------------------|-------------------|
| Prometheus (per replica) | 300m / 300Mi | 500m / 500Mi | 1000m / 1Gi |
| Grafana | 100m / 150Mi | 200m / 250Mi | 300m / 400Mi |
| Thanos Sidecar | 50m / 50Mi | 100m / 100Mi | 150m / 150Mi |
| Alertmanager (per replica) | 50m / 100Mi | 100m / 150Mi | 150m / 200Mi |

## Aggregated Totals

| Size  | Prometheus Replicas | Prometheus per Replica (CPU / Mem) | Grafana (CPU / Mem) | Thanos Sidecar (CPU / Mem) | Alertmanager (CPU / Mem) | Total Base (No HA, No Alertmanager) | Total With Alertmanager | Total With HA (2x Prometheus) | Total With HA + Alertmanager |
|-------|---------------------|-------------------------------------|---------------------|----------------------------|--------------------------|-------------------------------------|-------------------------|------------------------------|-------------------------------|
| small | 1 | 300m / 300Mi | 100m / 150Mi | 50m / 50Mi  | 50m / 100Mi  | 450m / 500Mi  | 500m / 600Mi  | 750m / 800Mi  | 800m / 900Mi  |
| medium| 1 | 500m / 500Mi | 200m / 250Mi | 100m / 100Mi| 100m / 150Mi | 800m / 850Mi  | 900m / 1Gi    | 1.3 / 1.35Gi  | 1.4 / 1.5Gi   |
| large | 1 | 1000m / 1Gi  | 300m / 400Mi | 150m / 150Mi| 150m / 200Mi | 1.45 / 1.53Gi | 1.6 / 1.72Gi  | 2.45 / 2.53Gi | 2.6 / 2.72Gi  |

Legend:
- Total Base = Prometheus (1 replica) + Grafana + Thanos Sidecar
- HA = Prometheus replicas = 2 (sidecar remains 1)
- “With Alertmanager” adds 1 replica of Alertmanager
- Thanos Query (if deployed separately) is NOT included

## Calculation Formulas

Let:
- P = Prometheus per replica
- G = Grafana
- T = Thanos sidecar
- A = Alertmanager per replica

Then:
- Base = P + G + T
- Base + Alertmanager = Base + A
- HA = (2 × P) + G + T
- HA + Alertmanager = (2 × P) + G + T + A

Example (small, HA + Alertmanager):
- CPU: (2 × 300m) + 100m + 50m + 50m = 800m
- Memory: (2 × 300Mi) + 150Mi + 50Mi + 100Mi = 900Mi

## Multiple Alertmanager Replicas

The table assumes 1 Alertmanager replica (common default).  
If deploying 2 replicas, add one more Alertmanager footprint:

| Size  | Add CPU | Add Memory |
|-------|---------|------------|
| small | +50m    | +100Mi     |
| medium| +100m   | +150Mi     |
| large | +150m   | +200Mi     |

Adjusted HA + 2×Alertmanager formula:
```
Total = (2 × P) + G + T + (2 × A)
```

Example (medium, HA + 2×Alertmanager):
- CPU: (2 × 500m) + 200m + 100m + (2 × 100m) = 1.5 CPU
- Memory: (2 × 500Mi) + 250Mi + 100Mi + (2 × 150Mi) = 1.65Gi

## Storage Considerations

- Prometheus PVC per replica: 10Gi (fixed default in current platform profile)
- With HA enabled: total raw Prometheus storage = 20Gi
- Retention (`monitoringStack_retention`) must fit within allocated capacity
- Reducing retention lowers space pressure

If nearing storage exhaustion:
1. Lower retention (e.g., `7d` → `3d`)
2. Reduce scrape intervals (requires chart-level changes)
3. Contact platform operators for storage expansion

## Validation Commands

Verify actual applied requests after deployment:

```bash
# Prometheus containers
oc get sts -l app.kubernetes.io/name=prometheus -n <team-namespace> \
  -o jsonpath='{range .items[*].spec.template.spec.containers[*]}{.name}{" => "}{.resources.requests.cpu}{" / "}{.resources.requests.memory}{"\n"}{end}'

# Grafana
oc get deploy -l app.kubernetes.io/name=grafana -n <team-namespace> \
  -o jsonpath='{range .items[*].spec.template.spec.containers[*]}{.name}{" => "}{.resources.requests.cpu}{" / "}{.resources.requests.memory}{"\n"}{end}'

# Alertmanager (if enabled)
oc get sts -l app.kubernetes.io/name=alertmanager -n <team-namespace> \
  -o jsonpath='{range .items[*].spec.template.spec.containers[*]}{.name}{" => "}{.resources.requests.cpu}{" / "}{.resources.requests.memory}{"\n"}{end}'
```

## When to Upsize

| Symptom | Action |
|---------|--------|
| Frequent Prometheus OOMKills | Increase size (e.g., small → medium) |
| High query latency | Increase size or enable HA |
| Retention shorter than expected | Reduce ingestion or request storage increase |
| Gaps in metrics under load | Enable HA or raise Prometheus resources |
| Alerts delayed | Check Alertmanager CPU/memory or increase size |

## Summary

- Start with `small` for development
- Move to `medium` for production with moderate load
- Use `large` for high ingestion or query volume
- Enable HA for critical production monitoring continuity
- Add Alertmanager only if you integrate notifications

Return to the main feature page: [Observability](./observability.md)
