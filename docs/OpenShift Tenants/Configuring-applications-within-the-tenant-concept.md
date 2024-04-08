---
Author: Oskar Marthinussen, Marcus Notø
Title: Configuring Applications Within The Tenant Concept
Version: 1.0.0
externally-exposed: false
--- 


# Configuring Applications Within The Tenant Concept


## Introduction

This user guide provides a template for configuring applications within your developer tenant. Initially, the guide will explain how to set up all applications in your main repository. Subsequently, it will introduce an alternative approach using multiple repositories for sub-applications.

Below is an example of how you can structure your tenant application:

![image.png](../img/Openshift%20Tenants/OpenShiftApplication1.png)

This picture illustrates how applications are deployed in your OpenShift tenant. When creating a tenant, several resources will be configured in OpenShift. One essential resource generated is the ArgoCD application set. For every environment present in a tenant, e.g., `tenant-test` and `tenant-dev`, an individual ArgoCD application will be created. In these environments, you input your application code, which can be sourced from the main repository and include code from additional external repositories.


## Setup for main repository

The application set references a specific `repoURL`, which acts as the main repository for the tenant. This URL is standard across all environments. Applications for particular environments are in the repository under the path `/<env>/application`. For more information about tenant variables, see the user guide [Order OpenShift Tenant](/docs/OpenShift%20Tenants/OrderOpenShiftTenant.md). 

Below is an example of how to configure the main repository:

![image.png](../img/Openshift%20Tenants/OpenShiftApplication2.png)

### Declaring New Applications

The directory `<env>/applications` is where new applications are declared and deployed via a `kustomization.yml` file. When introducing a new application, it's essential to create a file characterized by `kind: Application`.

Below is an example template that can be used when deploying new applications:

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: <application_name>
  namespace: gitops-developers
spec:
  destination:
    namespace: <tenant_name>-<env>
    server: 'https://kubernetes.default.svc'
  source:
    repoURL: 'https://github.com/<organization>/<repository>.git'
    path: <application_name>
    targetRevision: HEAD
  syncPolicy:
    automated:
      selfHeal: true
      prune: true
      allowEmpty: true
  project: <tenant_name>
```

### Using Multiple Repositories for Sub-applications

When configuring your tenant application, you might need to integrate additional repositories. This guide outlines the process for incorporating sub-applications from external repositories into your main repository setup.

It's important to note that sub-applications aren't required to be in the same repository as the main tenant application. The repository details can be specified using `spec.source.repoURL` in the application's configuration file.

For private repositories, credentials are essential. You can introduce them as a sealed secret within the directory where applications are defined. For further guidance on sealing secrets, please refer to [Encrypting secrets with Kubeseal](/SolidCloud/SolidCloud-Products/Containers/Red-Hat-OpenShift/User-Guides/3%2DSecret-Management-on-OpenShift/2%2DEncrypting-secrets-with-Kubeseal).

Below is an example of setting up your main repository:


![image.png](../img/Openshift%20Tenants/OpenShiftApplication3.png)

Below is an example of how the  private repository should be set up:

![image.png](../img/Openshift%20Tenants/OpenShiftApplication4.png)




