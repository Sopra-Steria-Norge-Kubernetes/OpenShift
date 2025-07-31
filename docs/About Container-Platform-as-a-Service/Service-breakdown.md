---
Author: Marcus Notø
title: Container Platform Service Variants breakdown
author: Sopra Steria
version: 0.1.0
---

# Service breakdown

Sopra Steria’s Container Platform as a Service (CPaaS) is offered in several variants, allowing organizations to choose the right level of isolation, functionality, and operational responsibility.

Each variant builds on a common foundation of managed Kubernetes (OpenShift) clusters, delivered with integrated platform services and tailored to fit different customer needs—from minimal clusters to fully integrated development environments.

## Overview of Variants

| Service                         | Description |
|----------------------------------|-------------|
| **Dedicated Container Platform** | A fully isolated, customer-specific container platform for hosting and managing multiple Kubernetes or OpenShift clusters. Ideal for sovereign cloud or high-compliance environments. Includes full control and the ability to add optional services. |
| **Cluster as a Service**         | A minimal, standalone Kubernetes or OpenShift cluster managed by Sopra Steria. Includes the core platform foundation—lifecycle, networking, security, logging, and storage. |
| **Cluster as a Service Premium** | A managed cluster with built-in developer environments (Tenants), GitOps pipelines, identity management, observability, and policy enforcement. Designed to be production-ready from day one. |

---
# Service Breakdown

![alt text](../img/About Container-Platform-as-a-Service/service_overview.png)

The table below shows what is **included** by default in each service variant, and what is available as an **optional add-on**.

| Service Component                              | Dedicated Platform | Cluster as a Service | Cluster as a Service Premium |
|------------------------------------------------|---------------------|-----------------------|-------------------------------|
| **Platform isolation (Dedicated clusters)**     | ✅ Yes               | ❌ No                 | ❌ No                         |
| **Cluster lifecycle management**                | ✅ Included          | ✅ Included           | ✅ Included                  |
| **Networking (Ingress and API access)**         | ✅ Included          | ✅ Included           | ✅ Included                  |
| **OIDC authentication and RBAC**                | ✅ Included          | ✅ Included           | ✅ Included                  |
| **Persistent storage (PVC support)**            | ✅ Included          | ✅ Included           | ✅ Included                  |
| **Monitoring and logging**                      | ✅ Included          | ✅ Included           | ✅ Included                  |
| **Security and compliance (baseline)**          | ✅ Included          | ✅ Included           | ✅ Included                  |
| **Cluster admin/operator services**             | ✅ Included          | ✅ Included           | ✅ Included                  |
| **Self-service access (API & GUI)**             | ✅ Included          | ✅ Included           | ✅ Included                  |
| **Application Tenants (runtime environments)**  | 🔁 Optional          | ❌ Not included       | ✅ Included                  |
| **GitOps / CI/CD integration**                  | 🔁 Optional          | ❌ Not included       | ✅ Included                  |
| **Private container registry**                  | 🔁 Optional          | ❌ Not included       | ✅ Included                  |
| **Team Landing Zones**                          | 🔁 Optional          | ❌ Not included       | ✅ Included                  |
| **Backup services**                             | 🔁 Optional          | ❌ Not included       | ✅ Included                  |
| **Advanced security services (e.g., ACS)**      | 🔁 Optional          |  🔁 Optional      | ✅ Included                  |

> ✅ Included = Standard delivery  
> 🔁 Optional = Available as add-on (in Dedicated variant)  
> ❌ Not included

---

For more technical or architectural details, or help selecting the right model, please contact your Sopra Steria advisor.


