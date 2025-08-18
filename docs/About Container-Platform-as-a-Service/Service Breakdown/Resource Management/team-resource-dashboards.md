---
Author: Simen Haga
Title: Team Resource Dashboards
Version: 1.0.0
externally-exposed: true
--- 

# Team Resource Dashboards

## Introduction

This section provides an overview of the default Team Resource Dashboards that comes with your team’s Grafana instance. 

These dashboards serve as both a resource management and FinOps overview, helping you visualize utilization, optimize costs, and right-size your workloads for improved efficiency.

### Pre-requisites

- [Setting up Grafana for teams](../../../OpenShift%20Teams/Team%20features/observability/observability.md)

## Dashboards

### Team Overview

This dashboard shows your team’s CPU and memory usage and the resulting costs across all tenants. You can adjust the “vCPU price” variable at the top to update the per-core rate and see the impact on total CPU cost. It also includes a breakdown of quota usage and waste percentage for each tenant.

![team-overview-all-tenants](../../../img/Resource%20Management/team-overview-all-tenants.png)

### Resource Optimization

This dashboard gives a historical view of resource use and cost savings for a single tenant. It shows the number of namespaces, hard vs. used CPU quota (and what’s left), the resulting total CPU cost, and estimated monthly savings from right-sizing. Below, a table lists each workload’s current requests alongside optimized values and the CPU/memory savings (with NOK savings calculated).

![team-overview-all-tenants](../../../img/Resource%20Management/resoruce-optimization.png)

The four dashboard variables at the top work as follows:

- **Tenant:** pick which tenant to analyze
- **vCPU price:** set the per-core rate to recalculate cost
- **CPU Percentile:** choose the usage percentile (e.g. 95%) for sizing recommendations. Meaning, in 95% of the cases, your CPU request will be sufficient. 
- **Overhead percentage:** add a buffer to your CPU percentile. Meaning you get the CPU Percentile + X% overhead.