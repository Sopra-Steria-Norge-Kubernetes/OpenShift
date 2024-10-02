---
Author: Marcus Notø, Simen Haga
Title: OpenShift GitOps Best Practices
Version: 0.0.1
externally-exposed: false
---

# OpenShift GitOps Best Practices

## Introduction
This document aims to describe how to use best practises in ArgoCD and GitOps and how you as a developer can leverage these practices to speed up your GitOps workflow. 
The document also describes the different sync possiblities avaialable in ArgoCD, and how and when to use them.

## ArgoCD configuration

!!! info
    The ArgoCD Application Controller is set to auto-sync every **6 minutes**.

Read how to configure ArgoCD for your tenant here:

- [How to configure Argo CD](../OpenShift%20Tenants/Tenant%20features/GitOps/argocd.md)

## ArgoCD Workflow

1. Make your code changes in your repository connected to ArgoCD
    1. ArgoCD ApplicationsSets syncs resources from the source repository

2. The Application Controllor will sync your newly pushed changes after 6 minutes. But if you dont like to wait, follow the next steps.
3. Navigate to you ArgoCD GitOps Developers URL and find you application where you have made the code changes.
    3. In the application view, hit `REFRESH`. A normal refresh checks if your source has changed, e.g. if there was a new commit to Git, or if there's a new version of the Helm chart in the registry.
    3. If nothing happens, hit `HARD REFRESH`. A hard refresh invalidates the manifest cache for your application, forcing the manifests to be re-rendered no matter whether there was a change in the source or not.
    3. If there is still nothing happening, try hitting `SYNC`. Sync reconciles the current cluster state with the target state in git.

4. Wait for the changes to finish syncing
     
    