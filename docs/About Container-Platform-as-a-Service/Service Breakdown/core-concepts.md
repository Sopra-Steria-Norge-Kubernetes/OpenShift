---
Author: Marcus Notø
title: Core concepts - OpenShift Teams and Tenants
author: Sopra Steria
version: 0.1.0
---

# Core Concepts - OpenShift Teams and Tenants

## Overview

OpenShift Container Platform as a Service consists of three key concepts to deliver a standardise environment to developers:

- **Clusters**: A Kubernetes/OpenShift cluster to run containers which is ordered from Sopra Steria as a managed service. 
- **OpenShift Teams**: A logical “landing zone” within a cluster that provides shared resources and centralized management to multiple runtime Environemnts called **_OpenShift Tenants_** 
- **OpenShift Tenants**: Isolated runtime environments dedicated to a single application or microservices, with their own resources, configurations, and access controls.


Understanding the relationship between these concepts is essential for making full use of Sopra Steria’s framework and accelerating your time to market.  
![Team-Tenant Correlation](../../img/Developer%20Teams/team-tenant-corraltion.png)

## What are OpenShift Teams?

**OpenShift Teams** are a management layer that sits above OpenShift Tenants, providing shared resources and centralized administration for a teams microservices. It is used to manage multiple OpenShift Tenants and their namespaces and projects. 

### Features and Benefits

- **Observability Dashboards**: Grafana instance with datasources for all team tenants, enabling unified monitoring across all team applications.
- **Secret Management**: ClusterSecretStores accessible by all team tenants, reducing duplication by sharing credentials and configurations.
- **GitOps Credentials**: Shared repository access credentials for ArgoCD, simplifying administration by managing multiple tenants from one central location.
- **Consistent Policies**: Apply team-level policies to all member tenants, ensuring uniform governance and streamlined management.

### Getting Started with OpenShift Team 

**Quick Start**: [OpenShift Teams](../../Quick Start/teams-quick-start.md)


## What are OpenShift Tenants?

**OpenShift Tenants** are isolated runtime environments dedicated to single applications or microservices. Each tenant provides its own resource quota shared among the containers running within its runtime environments, ensuring resource isolation and management.

![TenantOverview](../../img/About Container-Platform-as-a-Service/TenantOverview.png)


### Features and Benefits

- **Backup Service**: Define backup schedules, specify which resources are backed up, and configure retention policies to ensure data protection and business continuity.
- **Runtime Environments**: OpenShift namespaces configured with resource quotas and network policies to isolate workloads, protecting applications from interference.
- **Resource Management**: Enforce resource quotas and limits to optimize usage and cost, guaranteeing CPU and memory resources for predictable performance.
- **Role-Based Access Control (RBAC)**: Apply least-privilege access controls to secure tenant resources, enabling granular and secure operations.
- **Network Traffic Rules**: Manage ingress and egress traffic via defined lists of allowed URLs and IP sets, controlling access to and from tenant applications for enhanced security.
- **Secret Management**: Integrate with Azure KeyVault for secure dynamic retrieval of application secrets; alternatively, use Bitnami Sealed Secrets for version-controlled secret management, centralizing and securing sensitive information.
- **Grafana Dashboards**: Optionally provision dedicated Grafana instances for customized monitoring and observability, supporting tenant-specific insights.
- **GitOps Integration**: Use Git repositories to define and version-control tenant resources, enabling streamlined continuous deployment with ArgoCD and facilitating rapid releases.

###  Getting Started with OpenShift Tenant

**Quick Start**: [OpenShift Tenants](../../Quick Start/tenants-quick-start.md)