---
title: Roles and Responsibilities
author: Marcus Notø
version: 1.0.0
externally-exposed: false
--- 

# Roles and Responsibilities

## Introduction
This document outlines the roles and responsibilities for Container platform as a Service (CPaaS) on a OpenShift Container Platform (OCP). It's designed to improve communication, boost collaboration, and clarify expectations for all stakeholders. Understanding these roles and duties is vital for the product's efficiency and success. All stakeholders should review this guide to understand their contributions better.


<!-- ![overview](../img/About%20Container-Platform-as-a-Service/rolesAndResponsibilities.png) -->

## Roles
- **Sopra Steria (Provider):** Sopra Steria is entrusted with its provision, management, and optimization, ensuring seamless alignment with industry standards and user requirements for the OCP. Sopra steria is also responsible for developing the Tenant concept for the OCP and help facilitate onboarding of new customer Tenants. 
- **Developer/User (Customer):** Representing the end-users, these individuals leverage the platform for their developmental needs. Their active engagement and feedback are instrumental in refining the platform to better cater to user needs. This role is the consumer of the OCP Tenants.

## Responsibilities 

Below is a summary of the responsibilities of the different roles:


| Area | Sopra Steria (provider) | Developer/user (customer) | Additional Description |
|------|--------------|---------------------------|------------------------|
| **Underlying Infrastructure** | -- | -- | -- |
| Deploy and manage underlying infrastructure | ✔️ | | |
| Lifecycle management of underlying infrastructure | ✔️ | | |
| **Kubernetes Cluster** | -- | -- | -- |
| Deploy and configure OpenShift Container Platform (OCP) foundation | ✔️ | | |
| Deploy and configure OpenShift clusters | ✔️ | | |
| Deploy and configure OpenShift cluster-operators |✔️  | | Cluster-operators are considered operators that are needed to configure and manage the OCP.|
| Deploy and configure OpenShift operators for managed add-ons |✔️  | | |
| Deploy and configure cluster-scoped application operators |✔️ | ✔️ | Cluster-scoped application operators provide self-service features for developers. Developers are responsible for maintaining operator instances. |
| Deploy and configure OpenShift namespace-scoped application operators | | ✔️ |  Namespace-scoped application operators provide self-service features for developers. Developers are responsible these operators.  |
| Build and deploy applications in the OpenShift clusters | | ✔️ | |
| **Kubernetes Lifecycle management** | -- | -- | Lifecycle management of OpenShift cluster refers to the comprehensive process of planning, deploying, maintaining, updating, and eventually decommissioning an OpenShift cluster throughout its entire life span.|
| Lifecycle management of OpenShift cluster |✔️ |  | |
| Lifecycle management of OpenShift operators for infrastructure |✔️ |  | |
| Lifecycle management of OpenShift cluster-scoped operators for applications | ✔️ | | |
| Lifecycle management of OpenShift namespace-scoped operators for applications | |✔️ | |
| **Capacity management** | -- | -- | -- |
| Monitor underlying storage capacity | ✔️ |  | Sopra Steria will continuously monitor the OCP nodes to ensure optimal performance and scalability. |
| Monitor underlying compute capacity | ✔️ |  | Sopra Steria will continuously monitor the OCP nodes to ensure optimal performance and scalability. |
| Increase/Decrease storage and compute resources as needed | ✔️ |  | Sopra Steria will manage and adjust resources based on capacity needs to maintain platform performance. |
| Monitoring application consumption of resource in accordance with the tenant resource quota.|  | ✔️ | Each customer is responsible for monitoring the limits and quotas of their tenant. Sopra Steria has provided guidelines for how this should be done.  |
| Ordering increased Tenant storage and compute capacity |  | ✔️ | Customers are responsible for requesting more resources as needed based on their monitoring and demands. |
| Increasing Tenant storage and compute capacity based on customers orders |  ✔️  || When the customers order more or less storage and compute capacity, Sopra steria is responsible for configuring the Tenants Quotas and limits.  |
| **Network and connectivity** | -- | -- | -- |
| Network Infrastructure Setup | ✔️ |  | Setting up and maintaining the underlying network infrastructure and the connection to Solid Cloud 2 |
| Network Managementt towards Sopra Steria components | ✔️ |  | Establishing and maintaining firewall rules towards Sopra steria managed resources.   |
| Network Connectivity Establishment | ✔️ |  | Establishing connections to customer components and other external components (Internet and 3rd parties). |
| Requesting and Ordering Network Resources |  | ✔️ | Ordering required firewall rules and other network resources for customer components outside of OCP. |
| Assessing Network Security Measures* | ✔️ | ✔️  | Evaluating and implementing security measures for the connections to external components. |
| Implementing need for external connectivity based on customer orders |✔️  |  | Ordering needed firewall rules and network security policies to customer resources outside OCP.|
| Load Balancing Implementation* | ✔️ |  | Implementing and managing load balancers for traffic distribution towards the OCP cluster. |
| Network Monitoring and Logging* | ✔️ |  | Monitoring network traffic and logging for audit and troubleshooting purposes. |
| DNS Management | ✔️ | | Managing DNS records for services running in the cluster. |
| Order DNS records for custom routes| | ✔️ ||
| Ingress and Egress Controllers | ✔️ |  | Setting up and managing incoming and outgoing traffic controllers. |
| Implementation and management of internal OpenShift network |✔️  | | |
| Expose applications on ingress and other applications on the OCP | | ✔️ | |
| Network Policy Management |  | ✔️ | Defining and implementing network policies controlling pod communication. |
| **Desired state configuration** | -- | -- | -- |
| Deploy and manage desired state configuration tools such as ArgoCD | ✔️ | |  ArgoCD practices the "GitOps" paradigm, where a Git repository serves as the source of truth for defining the desired state of applications and infrastructure.|
| Deploy and manage developer Tenants based on customers orders| ✔️ | | Tenants are not native OpenShift concepts but act as wrappers around multiple OpenShift resources, offering a standardized development workflow, scalable resource access, and appropriate limits and access controls for each team.|
|Connecting the Customer GIT repository in a tenant towards the GitOps tool ArgoCD managed by Sopra Steria | | ✔️ |ArgoCD continuously monitors and compares the actual state of the Kubernetes cluster (as deployed resources) with the desired state defined in the Git repository |
| Deploy applications from GIT Repository managed in the developers Tenant | | ✔️ | |
| Application Connectivity |  | ✔️ | Ensuring proper communication configurations for applications. |
| Custom Network Policies |  | ✔️ | Defining specific network policies as per application requirements. |
| Service Exposition Methods |  | ✔️ | Deciding how services are exposed outside the cluster. |
| Custom DNS Configurations for Applications|  | ✔️ | Providing any custom DNS configurations for applications running in the developer tenant.
| **Storage capabilities and Persistent volume** | -- | -- | -- |
| Ensuring the underlying storage infrastructure's health, performance, and availability. | ✔️| | |
| Providing storage classes with  Read-Write-Many (RWX) capabilities | ✔️| | |
| Providing storage classes with  block and filesystem capabilities | ✔️| | |
| Creating and implementing needed Persistent Volumes (PVs) for applications | ✔️ | | |
| Creating and implementing needed Persistent Volumes Claims (PVCs) for applications | |✔️  | |
| **Access Control** | -- | -- | -- |
| Configuring and managing OIDC with Azure AD as the identity provider in OCP | ✔️| | |
| Registering the Azure AD OIDC enterprise application for OCP and providing Sopra Steria with the configuration details | | ✔️ | |
| Managing OIDC token and session lifecycles | ✔️| | |
| Configure standard Role-Based Access Control (RBAC) | ✔️|  | |
| Synchronizing Azure AD groups and users with OCP based on configuration in Customers Tenants | ✔️| | |
| Provide Azure AD groups in the developer Tenants to be synchronised and assign users in AD groups.  | | ✔️ | |
| **Private registry** | -- | -- | -- |
| Deploy and manage private registry foundation |✔️  | | |
| Backup and restore of container images and artefacts | ✔️ | | |
| Consume private registry | | ✔️ | |
| **Backup services** | -- | -- | -- |
| Deploy and manage backup service foundation | ✔️ | | Regularly verifying that backups are valid and restorable.|
| Regularly verify the integrity of backups* | ✔️ | | |
| Regularly test restore capabilities of backups* | ✔️ | | |
| Monitor backup jobs and setup alerts for failures* | ✔️ | | |
| Configure off-site backup | ✔️ | | |
| Provide a Off-site backup target | | ✔️ | |
| Performe off-site restore | ✔️ | | |
| Agree on backup policy and schedule | ✔️ | ✔️ | This includes establishing how long backups are stored before being deleted or archived. |
| Establish, run and schedule local backup |✔️  | | |
| Restore local backup |✔️  | | |
| **Kubernetes monitoring and logging** | -- | -- | -- |
| Deploy and manage monitoring and alerting foundation |  ✔️| | |
| Configure monitoring and alerting of cluster health |✔️ |  | |
| Facilitate the possibility of standard monitoring and alerting of applications |✔️ |  | |
| Configure standard monitoring and alerting for applications | | ✔️ | |
| Configure custom monitoring and alerting for applications | | ✔️ | |
| Monitor Kubernetes resources (pods, nodes, services) |✔️ |  | |
| Monitor user access and activities in the cluster |✔️ |  | |
| Monitor user access and activities in tenants |✔️ |  | |
| Conduct performance benchmarking for applications| | ✔️ | |
| Create and manage monitoring dashboards for OCP |✔️ |  | |
| Create and manage standard monitoring dashboards for applications in Tenants|✔️ |  | |
| Create and manage custom monitoring dashboards for applications in Tenants|✔️ |  | |
| Deploy and manage logging foundation |✔️ |  | |
| Configure export to external log endpoint |✔️  | | |
| Provide log search dashboard for infrastructure components | ✔️ | | |
| Configure custom logging and dashboards for applications | | ✔️ | |
| Analyze logs for anomalies and unexpected activities* |✔️ |  | |
| Integrate critical alerts with incident management systems | ✔️|  | |
| **Security and compliance** | -- | -- | -- |
| Deploy and manage security foundation |✔️ |  | |
| Report compliance metrics* |✔️ |  | |
| Send security events to customer SOC* | ✔️ | | |
| Enabling scanning of security vulnerabilities in developer applications* | ✔️| | |
| Scan for security vulnerabilities in developer applications* | |✔️  | |
| Handle security vulnerabilities in developer applications | |  ✔️| |

* Responsibilities that are not ready now must be implemented before total production.

