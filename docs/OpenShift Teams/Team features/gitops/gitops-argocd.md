# GitOps - Argo CD Applications & Applicationsets

On this page you can find information related to the `gitops.argocd` feature in the team concept. This page contains an example of how to implement the Argo CD applications & Applicationsets

### How to Configure ArgoCD Applicationset

Below we will go through an example on how to implemet the gitops argocd feature in the team chart. The example utilizes the default values for `SyncPolicies`, `resource_name_first`, and `custom_target_revision`.

!!! Info
    To use the gitops argocd feature you will also need to define the login credentials Argo CD will use to access the repository

    How to configure gitops authentication: [GitOps - Auth & Creds](./gitops-authentication.md)


Below is an example of how the `gitops.argocd` section for defining applicationset details can look like:

```yaml
gitops:
  argocd:
    enable_auto_defined_apps: true
    team_repo_url: https://github.com/customer-repo/openshift
    path: "/path/to/applications"

```

Below is all possible fields to configure for the `gitops.argocd` feature

```yaml
gitops:
  argocd:
    enable_user_defined_apps: <Enable creating applications with the user-defined method- app of apps (true/false). Defualt false>
    enable_auto_defined_apps: <Enable using automatic application creation with an ArgoCD applicationsets per environment(true/false). Defualt true >
    team_repo_url:  <Git repository url that GitOps (ArgoCD) will use as its "source of truth" for the team namespace> 
    path: <Path to the folder that contains infrastructure that the applicationsets will insert into the team namespace>
    syncPolicy:
      allowEmpty: <Allows ArgoCD to sync an ApplicationSet even if it results in an empty application (true/false). Default true>
      selfHeal: <Automatically repair out-of-sync resources to match the desired state in Git (true/false). Default true>
      prune: <Remove resources that are not present in the Git repository during sync (true/false). Default true>
    resource_name_first: <Nameingstandard for ArgoCD applications created by applicationSets. If true the name of the resource (folder) will come first if false then the name of the team will come first. Default true>
    custom_target_revision: <Allows setting the targetRevision at the application level for different environments in OpenShift. The generator picks up component names and creates targetRevision values based on the application folder name instead of using HEAD if set to true. Default false>
```

## In-depth description of parameters

The `gitops` feature in the team concept have the same functionality as in the tenant concept´s `argocd` feature.

As in the tenant concept you also have the ability to enable the user-defined method and the auto-defined method.

1. **User-defined method:** Create ArgoCD applications in your repository under the path `<path>/<all argocd apps here will be generated>`.
    - To enable this choice, you must set the field `gitops.argocd.enable_user_defined_apps` to true.

2. **Auto-defined method:** Use an ArgoCD applicationSet to create your applications automatically under the path `<path>/<all folders here will be generated>`.
    
    - To enable this choice you have to set the  `gitops.argocd.enable_auto_defined_apps field` to true. This will create an ApplicationSet for the team namespace and will configure new applications when you add new folders in your team folder.

More information about how to set up a Git Repository for ArgoCD on OpenShift can be found here:

* [OpenShift GitOps - Introduction](../../../About%20Container-Platform-as-a-Service/Service%20Breakdown/GitOps/Introduction-GitOps.md) 

| <div style="width:140px">**Variable**</div>            | **Description**                                                                                                     | **Example**                                | **Type**                  | **Default Value**  |
|----------------------|---------------------------------------------------------------------------------------------------------------------|--------------------------------------------|---------------------------|------------|
| SyncPolicy              |                                                                                                                     |                                            |                           |
| `allowEmpty`            | Allows ArgoCD to sync an ApplicationSet even if it results in an empty application                               | True / False | Boolean                    | true |
| `selfHeal`            | Automatically repair out-of-sync resources to match the desired state in Git                                        | True / False | Boolean                    | true |
| `prune`           | Remove resources that are not present in the Git repository during sync                                                | True / False                                 | Boolean                    | true |
| Argo CD Configuration              |                                                                                                                     |                                            |                           |
| `team_repo_url`            | The URL of the git repository which ArgoCD will use as its "source of truth"                                     | https://github.com/customer-repo/openshift | String                    | "" |
| `path`      | The path specifying where Argo CD will search for applications/applicationsets                                                          | /path/to/applications                      | String | "" |
| `resource_name_first`     | Nameingstandard for ArgoCD applications created by applicationSets. If true the name of the resource (folder) will come first if false then the name of the team will come first.                                                        | True / False                      | Boolean | True |
| `custom_target_revision`     | Allows setting the targetRevision at the application level for different environments in OpenShift. The generator picks up component names and creates targetRevision values based on the application folder name instead of using HEAD if set to true                                                        | True / False                      | Boolean | False |