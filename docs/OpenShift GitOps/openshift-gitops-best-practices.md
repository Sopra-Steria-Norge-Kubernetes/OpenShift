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

1. 