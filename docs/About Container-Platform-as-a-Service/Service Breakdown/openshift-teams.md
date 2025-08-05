# OpenShift Teams 

## What is a Team?

**Teams** are organizational units that enable centralized management of multiple related tenants. Teams provide shared resources and configurations that simplify administration for development teams managing multiple applications or microservices.

When a team is created in OpenShift, a dedicated namespace is established where all common secrets, service accounts, and shared resources are managed centrally.

## Team-Tenant Relationship

Teams act as a **management layer** above tenants:

Below is an illustration of teams.
![Team-Tenant Correlation](../../img/Developer%20Teams/team-tenant-corraltion.png)

## Team Features

### **Centralized Resource Management**
- **Observability Dashboards**: Grafana instance with datasources for all team tenants
- **Secret Management**: ClusterSecretStores accessible by all team tenants  
- **GitOps Credentials**: Shared repository access credentials for ArgoCD

### **Benefits for Development Teams**
- **Reduced Duplication**: Share credentials and configurations across tenants
- **Unified Monitoring**: Single Grafana instance for all team applications
- **Simplified Administration**: Manage multiple tenants from one central location
- **Consistent Policies**: Apply team-level policies to all member tenants

## Getting Started

**Quick Start**: [OpenShift Teams](../../Quick Start/teams-quick-start.md)