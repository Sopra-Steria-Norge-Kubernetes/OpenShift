# Monitoring Stack Sizing

This page details the estimated resource reservations for the team monitoring stack components (Prometheus, Grafana, Thanos sidecar, Alertmanager) across supported sizes and deployment modes (with / without HA, with / without Alertmanager). In most cases small stacksize should suffice, if you are unsure, use the small.

> These numbers reflect container **requests** (not limits).

## Component Baseline (Per Size)

| Component / Size | small (CPU / Mem) | medium (CPU / Mem) | large (CPU / Mem) |
|------------------|------------------|--------------------|-------------------|
| Prometheus (per replica) | 300m / 300Mi | 500m / 500Mi | 1000m / 1Gi |
| Grafana | 100m / 150Mi | 200m / 250Mi | 300m / 400Mi |
| Thanos Sidecar | 50m / 50Mi | 100m / 100Mi | 150m / 150Mi |
| Alertmanager (per replica) | 50m / 100Mi | 100m / 150Mi | 150m / 200Mi |

> Alertmanager run with 2 replicas (HA) whenever enabled. All totals that include Alertmanager already account for both replicas.

## Aggregated Totals

| Size  | Prometheus Replicas | Alertmanager Replicas | Prometheus per Replica (CPU / Mem) | Grafana (CPU / Mem) | Thanos Sidecar (CPU / Mem) | Alertmanager (2× CPU / Mem) | Total Base (No HA, No Alertmanager) | Total With Alertmanager (2×) | Total With HA (2× Prometheus) | Total With HA + Alertmanager (2×) |
|-------|---------------------|-----------------------|-------------------------------------|---------------------|----------------------------|-----------------------------|-------------------------------------|-------------------------------|----------------------------------|------------------------------------|
| small | 1 | 2 | 300m / 300Mi | 100m / 150Mi | 50m / 50Mi  | 100m / 200Mi  | 450m / 500Mi  | 550m / 700Mi  | 750m / 800Mi  | 850m / 1000Mi |
| medium| 1 | 2 | 500m / 500Mi | 200m / 250Mi | 100m / 100Mi| 200m / 300Mi | 800m / 850Mi  | 1000m / 1.15Gi | 1.3 / 1.35Gi  | 1.5 / 1.65Gi  |
| large | 1 | 2 | 1000m / 1Gi  | 300m / 400Mi | 150m / 150Mi| 300m / 400Mi | 1.45 / 1.53Gi | 1.75 / 1.92Gi | 2.45 / 2.53Gi | 2.75 / 2.92Gi |

Legend:
- Base = Prometheus (1) + Grafana + Thanos
- “With Alertmanager” = Base + 2× Alertmanager replicas
- HA = 2× Prometheus + Grafana + Thanos
- “HA + Alertmanager” = HA + 2× Alertmanager
- Thanos Query (if deployed separately) is not included

## Calculation Formulas

Let:

- P = Prometheus per replica
- G = Grafana
- T = Thanos sidecar
- A = Alertmanager per replica (always ×2 when enabled)

Then:

- Base (no Alertmanager) = P + G + T
- Base + Alertmanager = P + G + T + (2 × A)
- HA (no Alertmanager) = (2 × P) + G + T
- HA + Alertmanager = (2 × P) + G + T + (2 × A)

Example (small, HA + Alertmanager):

- CPU: (2 × 300m) + 100m + 50m + (2 × 50m) = 850m
- Memory: (2 × 300Mi) + 150Mi + 50Mi + (2 × 100Mi) = 1000Mi


Return to: [Observability](./observability.md)
