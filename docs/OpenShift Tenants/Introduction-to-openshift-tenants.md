# OpenShift Tenants

## Introduction to Tenants on OpenShift
This user guide explains the Tenant setup on the Container Platform as a Service (CaaS) provided by Sopra Steria with OpenShift. OpenShift is designed to support a single-tenant platform, making it challenging for cluster admins to host multi-tenancy within a single OpenShift cluster. Therefore the Tenant concept is introduced for CaaS.  Tenants are a powerful tool for achieving efficient resource utilization, reduced configuration efforts, and seamless sharing of cluster-internal resources among different Tenants and developer teams.

The figure below illustrates how two Tenants, A and B, exist on the same OpenShift platform.

![tenant](../img/Openshift%20Tenants/tenant.png)

Each Tenant has a pre-defined number of environments. In this example, the Tenants have two environments, a production and a Test namespace. With a Backup schedule, the developer team can decide to take a backup of their environment and ensure that it can be restored later. The tenant also has an ArgoCD workspace for each environment connected to the developer's team's git repository. 
 
## Access control with Tenants
Managing access control in Kubernetes, especially Role-Based Access Control (RBAC), can be complex and error-prone. The Tenant concept simplifies RBAC when integrated with an external identity provider (IDP) such as Azure AD. It provides ClusterRoles bound to tenant namespaces, enabling fine-grained control over access to namespaces and their resources. Furthermore, it integrates with existing OpenShift or external groups for seamless user management. 

The figure below illustrates how Azure AD groups are synced with the Tenant's environments.

![tenant_azure_ad](../img/Openshift%20Tenants/tenant_azure_ad.png)

In this example, there exist four Azure AD groups per tenant. Each namespace has one read group and one write group. The write group can modify and add new resources into the namespaces, and the read group can only view the resources. 

## Summary of advantages of Tenants on Openshift

A Tenant can be viewed as a workspace for a team in OpenShift, where they can develop one or more applications and further push them to their production environment. 
The Tenant concept gives the following features for organisations:

* **Simplified Multi-Tenancy:** Provides streamlined multi-tenancy within OpenShift.
* **Access Control:** Simplifies RBAC configuration with a "least privilege" mindset, automatically maintaining up-to-date rules with zero manual effort.
* **Self-Service:** Enables users to safely provision Kubernetes resources and objects for themselves and their teams. Team-owned namespaces and their resources count towards team quotas, simplifying resource allocation and sharing.
* **ArgoCD Integration:** Integrates with ArgoCD for streamlined RBAC management.
* **Cost and Resource Optimization:** Defines resource quotas at the tenant scope for efficient resource allocation.


## Next steps
Now that you are familiar with the Tenant concept, the next step is to start developing on OpenShift. 
If you haven't ordered a tenant from Sopra Steria, follow the user guide [Order OpenShift Tenant](Order-openshift-tenant.md) for more information on how this should be done.
