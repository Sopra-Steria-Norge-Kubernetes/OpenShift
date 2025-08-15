# OpenShift Tenants

OpenShift tenants provide isolated runtime environments for applications and microservices, each with its own resource quota for CPU and memory. This tenant model enables granular access control and efficient resource distribution among development teams, allowing teams to manage their allocated resources within defined limits.

## Features and Benefits

* **Backup Scheduling**: Teams can define backup schedules specifying which resources to back up and how long backups are retained, ensuring data protection and recovery readiness.

* **Isolated Runtime Environments**: Each tenant consists of a set of OpenShift namespaces serving as isolated environments for application deployment, enabling secure and independent operation of applications.

* **Resource Management**: A combined resource quota for memory and CPU capacity is allocated per tenant, ensuring efficient resource utilization and preventing resource contention.

* **Role-Based Access Control (RBAC)**: Access to tenant resources is managed through standardized roles linked to the customer’s Azure AD/Entra ID, following a least privilege approach to enhance security.

* **Network Policies**: Rules for incoming and outgoing traffic are defined via lists of URLs and IP sets, controlling service connectivity to and from tenant applications and improving network security.

* **Secrets Management**: Application secrets are securely stored and dynamically retrieved from Azure KeyVault, with Bitnami sealed secrets as a fallback, providing robust and version-controlled secret management.

* **Grafana Integration**: A dedicated Grafana instance can be set up for tenants to build custom dashboards, enabling tenant-specific monitoring and visualization.

* **GitOps Integration**: Git repositories can be used to define and version control the resources deployed within the tenant, streamlining deployment workflows and ensuring consistency.

![TenantOverview](../../img/About Container-Platform-as-a-Service/TenantOverview.png)

## Getting Started

**Quick Start**: [OpenShift Tenants](../../Quick Start/tenants-quick-start.md)