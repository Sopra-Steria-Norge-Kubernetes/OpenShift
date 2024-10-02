---
Author: Marcus Notø, Simen Haga
Title: Introduction to OpenShift GitOps
Version: 0.0.1
externally-exposed: false
---

# Introduction to OpenShift GitOps
OpenShift GitOps is a set of tools and processes that integrate OpenShift with GitOps practices, automating the continuous delivery (CD) of applications and infrastructure as code (IaC). Built on top of Argo CD, OpenShift GitOps enables users to manage, deploy, and monitor applications using Git repositories as the source of truth.

Developers can define application and environment configurations in a Git repository, and ArgoCD will ensure that the OpenShift cluster matches the defined state, deploying and updating applications automatically.

To configure GitOps for your Tenant the following must be done:
1. Connect your Tenant infrastructure repository to your Tenant in the Tenant definition. More information about this here
2. 

A developer must configure in their tenant definition to use `user-defined` and/or `auto-defined` application-creation. When this is set the developer must set up their repository to ligne with their choice of GitOps method. Below is a picture to illustra the process on a high-level:
![Alt text](../img/CI-CD/GitOps.png)