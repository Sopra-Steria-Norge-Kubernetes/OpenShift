# GitOps - Argo CD Applications & Applicationsets

On this page you can find information related to the `argocd` feature in the tenant concept. This page contains an example of how to implement the Argo CD applications & Applicationsets

### How to Configure ArgoCD Applicationset

Below we will go through an example on how to implemet the `argocd` feature in the tenant chart. The example utilizes the default values for `SyncPolicies`.

!!! Info
    To use the `argocd` feature you will also need to define the login credentials Argo CD will use to access the repository

    How to configure gitops authentication: [GitOps - Auth & Creds](./gitops-authentication.md)


Below is an example of how the `argocd` section for defining applicationset details can look like:

```yaml
argocd:
  enable_auto_defined_apps: true
  main_git_repository:
    repourl: "https://github.com/customer-repo/openshift" 
    path: "/path/to/applications"

```

Below is all possible fields to configure for the `gitops` feature

```yaml
argocd:
  enable_user_defined_apps_legacy: true
  enable_user_defined_apps: false
  enable_auto_defined_apps: true
  syncPolicy:
    allowEmpty: true
    selfHeal: true
    prune: true
  main_git_repository:
    repourl: "" 
    basepath: ""
```

## In-depth description of parameters

You can choose to enable the user-defined method or the auto-defined method (You can also choose both).

1. **User-defined method:** Create ArgoCD applications in your repository under the path `<path>/<all argocd apps here will be generated>`.
    - To enable this choice, you must set the field `argocd.enable_user_defined_apps` to true.

2. **Auto-defined method:** Use an ArgoCD applicationSet to create your applications automatically under the path `<path>/<all folders here will be generated>`.
    
    - To enable this choice you have to set the  `argocd.enable_auto_defined_apps field` to true. This will create an ApplicationSet for the tenant namespace and will configure new applications when you add new folders in your tenant folder.

More information about how to set up a Git Repository for ArgoCD on OpenShift can be found here:

* [OpenShift GitOps - Introduction](../../../About%20Container-Platform-as-a-Service/Service%20Breakdown/GitOps/Introduction-GitOps.md) 

| <div style="width:140px">**Variable**</div>         | **Description**                                                                                                     | **Example**                                | **Type**                  | **Default Value**  |
|----------------------|---------------------------------------------------------------------------------------------------------------------|--------------------------------------------|---------------------------|------------|
| SyncPolicy              |                                                                                                                     |                                            |                           |
| `allowEmpty`            | Allows ArgoCD to sync an ApplicationSet even if it results in an empty application                               | True / False | Boolean                    | true |
| `selfHeal`            | Automatically repair out-of-sync resources to match the desired state in Git                                        | True / False | Boolean                    | true |
| `prune`           | Remove resources that are not present in the Git repository during sync                                                | True / False                                 | Boolean                    | true |
| Default              |                                                                                                                     |                                            |                           |
| `repourl`            | The URL of the git repository which ArgoCD will use as its "source of truth"                                        | https://github.com/customer-repo/openshift | String                    | "" |
| `basepath`           | The basepath of the git repository where ArgoCD manifests are stored                                                | poseidon1/                                 | String                    | "" |
