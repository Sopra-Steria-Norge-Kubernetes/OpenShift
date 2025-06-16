---
Author: Per Kristian Svevad
Title: Order OpenShift tenant - Quick start guide
Version: 1.0.0
Target Audience: Customer - Onboarded developer or platform engineer
externally-exposed: true
--- 



# Order OpenShift tenant - Quick start guide

This quick-start user guide describes the simple steps to provide the minimum information Sopra Steria needs to create a new OpenShift tenant. 

An Openshift Tenant form is added to a collective Git repository and shared with Sopra Sterias OpenShift clusters. Different environments and resources are created in an OpenShift Cluster based on the content of the created tenant form.

A more in-depth description can be found here: [OpenShift Tenant Template](OpenShift-tenant-template.md)

## Pre-requisites
Before getting started with this guide, ensure you have the following prerequisites:

- You have planned your application name, resource requirements and the needed environments. 
- Your company is onboarded with the Sopra Steria tenant concept and has your organisation's Entra ID registration towards the Openshift Cluster. 
- Two groups in Entra ID, one for Read access and one for Write. 
- Have a Git repository with Personal Access Tokens (PAT), a GitHub Application or SSH. Detailed instructions for this is found here:
    - [PAT](../OpenShift%20Tenants/Tenant%20features/GitOps/Authentication%20Methods%20for%20ArgoCD/authenticate-with-personal-access-token.md)
    - [GitHub App](../OpenShift%20Tenants/Tenant%20features/GitOps/Authentication%20Methods%20for%20ArgoCD/authenticate-with-github-app.md)
    - [SSH](../OpenShift%20Tenants/Tenant%20features/GitOps/Authentication%20Methods%20for%20ArgoCD/authenticate-with-ssh.md)
- You have Kubeseal installed on your workstation and can provide encrypted values for the initial setup.
    - [Install Kubeseal](../Additional%20Documentation/Tools/install-kubeseal.md)

## OpenShift Tenant Template

In this guide, we will fill out the following form:
```yaml
appname: <Give your application an appname>
values: |
  namespace:
    name: <namespace to deploy kubernetes resources>
    description: <Give your namespace a description>
    requests:
      enable: true
      memory: <Specify memory limits for your application/project. e.g.: 2Gi>
      cpu: <Specify memory limits for your application/project. e.g.: 1>

  environments:
    - name: <First element in a list of the environments you want to create. e.g.: test>
    - name: <Second item on the list of environments. e.g.: prod>

  rbac:
    ad_group_write_access: <Entra ID group for write access>
    ad_group_read_access: <Entra ID group for read access>

  argocd: 
    enable_auto_defined_apps: true
    enable_user_defined_apps: true
    main_git_repository:
      repourl: https://github.com/<organization>/<repo>
      basepath: "</yourpath>"
      encrypted_url: <Encrypted repourl with sealedsecrets>
      encrypted_type: <value "git" encrypted with sealedsecrets>
      encrypted_password: <Your PAT encrypted with sealedsecrets>
      encrypted_username: <Username used with the PAT encrypted with sealedsecrets>
```

The values file for your Tenant needs to be sent to Sopra Steria through your defined ordering process.

