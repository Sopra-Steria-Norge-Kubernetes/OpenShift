---
Author: Marcus Notø, Simen Haga  
Title: OpenShift GitOps Best Practices  
Version: 0.0.1  
Externally Exposed: False
---

# OpenShift GitOps Best Practices

## Introduction
This document outlines the best practices for using ArgoCD within a GitOps workflow, designed to help developers streamline and optimize their processes. It also covers various sync options available in ArgoCD and provides guidance on when and how to use them effectively.

## ArgoCD Configuration

!!! info
    The ArgoCD Application Controller is configured to auto-sync every **6 minutes**. 

For detailed instructions on configuring ArgoCD for your tenant, refer to:

- [How to Configure Argo CD](../OpenShift%20Tenants/Tenant%20features/GitOps/argocd.md)

## ArgoCD Workflow

1. Make changes to the code in the repository connected to ArgoCD.
    - The ArgoCD ApplicationSets will automatically sync resources from the source repository.

2. By default, the Application Controller will sync your changes 6 minutes after they are pushed. If you'd prefer not to wait, follow these steps:
    1. Open your ArgoCD GitOps Developer URL and locate the application where you've made the code changes.
    2. In the application view, click `REFRESH`. A standard refresh checks if there have been changes in your source repository, such as a new commit or a new version of a Helm chart in the registry.
    3. If no updates appear, try clicking `HARD REFRESH`. This action invalidates the application's manifest cache, forcing the manifests to be re-rendered regardless of changes in the source.
    4. If the issue persists, click `SYNC`. This will reconcile the current cluster state with the desired state as defined in Git.

3. Wait for the synchronization process to complete.

## Options in ArgoCD

ArgoCD provides several options for managing applications, making it easier to monitor and maintain the resources in your cluster. Below is an overview of the key options:


### 1. **Sync**
   The `Sync` feature is used to synchronize the state of the cluster with the state defined in the Git repository. Here are the various options available under the Sync feature:

   - **Manual Sync**: Synchronizes the application only when you manually trigger the sync process. This gives you full control over when changes are applied.
   
   - **Auto-Sync**: Configures the application to sync automatically whenever a change is detected in the Git repository or Helm source. This is useful for continuous integration, where updates to applications are applied without manual intervention.

   - **Prune Resources**: When enabled, ArgoCD will remove Kubernetes resources that no longer exist in the Git repository. This ensures the cluster is always in sync with Git, removing outdated or unnecessary resources.

   - **Apply Out of Sync Only**: Synchronizes only the resources that are out of sync, instead of performing a full sync. This can save time if only a few resources have changed.

   - **Sync Waves**: Allows you to define the order in which resources are synchronized. For example, you can ensure ConfigMaps are updated before Pods are restarted.

   - **Hooks**: Sync hooks allow you to execute custom commands at different stages of the sync process (pre-sync, sync, post-sync). This gives you flexibility to perform custom actions as part of the sync.

   - **Retry**: You can configure ArgoCD to automatically retry syncing in case of failure, with the option to set a retry limit.

### 2. **Refresh**
   The `Refresh` option is used to update the application state from Git. This is useful for checking if there have been any changes without necessarily triggering a full sync.

   - **Soft Refresh**: Updates the application state based on changes in Git or Helm sources. It checks for updates but does not force a full cache refresh.

   - **Hard Refresh**: Invalidates the cache and forces a full update of the application's state by re-reading all resources from Git or Helm. This is useful if you suspect discrepancies between the cache and the actual state.
