---
Author: Marcus Notø, Simen Haga
Title: Introduction to OpenShift GitOps
Version: 0.0.1
externally-exposed: false
---

# Introduction to GitOps
OpenShift GitOps is a comprehensive toolset that integrates OpenShift with GitOps practices. Built on top of Argo CD, OpenShift GitOps enables teams to manage, deploy, and monitor applications using Git repositories as the single source of truth.


## Benefits of GitOps
With GitOps, developers define applications and infrastructure configurations in a Git repository using Infrastrucutre as Code (IaC) such as YAML defintions. ArgoCD will ensure that the OpenShift cluster matches the defined state, deploying and updating applications automatically. 



GitOps gives developer the following benefits:

- **Faster Release Cycles:** Automating deployments leads to faster, more efficient release processes.
- **Improved Reliability:** GitOps ensures consistency across environments, reducing configuration drift and human error.
- **Enhanced Security:** Git provides an auditable, secure source of truth, ensuring compliance with deployment policies.
  

## How GitOps works:
There are many ways of setting up GitOps with ArgoCD. Here we explain the recommended steps of how a developer can use OpenShif GitOps. The Tenant and git repository setup is explained in more detail under [GitOps Setup](gitops-setup.md).

The GitOps process follows these steps:

1. Define the tenant defintion with the wanted GitOps method. Push the configuration to your OCP Admin. Follow the setup given in the section [OpenShift Tenants - Quick Start](../OpenShift%20Tenants/Orderopenshift-tenant-quick-start-guide.md). 
2. A developer must decide if they want to use the `user-defined` and/or the `auto-defined`  method for application-creation. More information about these methods can be found under [GitOps Setup](gitops-setup.md#gitops-methods).  
3. Define Kubernetes resources in your *"Tenants Main Git repository"* (This is where your infrastructure code will be). Follow the examples given in [GitOps Setup](gitops-setup.md#getting-started).
4. The ArgoCD resources will then sync resources from the Tenants Main Git repository. It is important to define the correct target path in the repository. 
5. When the resources are synced from the main Tenant repository the resources will be created in the cluster with ArgoCD. 

 Below is a picture to illustrate the process on a high-level:
![Alt text](../img/CI-CD/GitOps.png)
 

## Best Practices for OpenShift GitOps with ArgoCD
ArgoCD is the preferred OpenShift GitOps tool supported by Red Hat. This tool can at first be difficult to use, but by following some best practices can be helpful. 

See the [OpenShift GitOps Best Practices ](openshift-gitops-best-practices.md) for more information.