# GitOps - Argo CD Feature

The gitops feature is used to define the login credentials for Argo CD to use aswell as the repository Argo CD will use for the Argo CD applicationsets and applications.

You can read more in depth about this feature by following this link: [ArgoCD](../../OpenShift%20Tenants/Tenant%20features/GitOps/argocd.md)

## How to configure Argo CD - Team Concept

Below is the configuration for setting up ArgoCD using our team concept:

```yaml
...  
  gitops:
    argocd:
      enable_user_defined_apps: <Enable creating applications with the user-defined method- app of apps (true/false)>
      enable_auto_defined_apps: <Enable using automatic application creation with an ArgoCD applicationsets per environment>
      team_repo_url: ""
      path: ""
      syncPolicy:
        allowEmpty: <Allows ArgoCD to sync an ApplicationSet even if it results in an empty application (true/false)>
        selfHeal: <Automatically repair out-of-sync resources to match the desired state in Git (true/false)>
        prune: <Remove resources that are not present in the Git repository during sync (true/false)>
      resource_name_first: true
      custom_target_revision: false
    team_git_repositories:
    - repourl: "" 
      encrypted_url: <The url of the git repository encrypted with sealedsecrets>
      encrypted_type: <Type should always be git, but must encrypted with sealedsecrets>
      credentials:
        github_app: 
          enable_app: <Enable GitHub App to authenticate ArgoCD with your Git Repository. Default false.>
          id: <The app id for your GitHub App encrypted with sealedsecrets>
          installation_id: <The installation id for your GitHub App encrypted with sealedsecrets.>
          private_key: <Private key for your GitHub App encrypted with sealedsecrets.>
        ssh_key:
          enable_ssh_key: <Enable SSH to authenticate ArgoCD with your Git Repository. Default false.>
          private_key: <Private key for your SSH-private-key encrypted with sealedsecrets.>
        pat:
          enable_pat: <Enable username and PAT to authenticate ArgoCD with your Git Repository. Default false.>
          username: <Username used with PAT encrypted with sealedsecrets>
          password: <PAT encrypted with sealedsecrets>
...    
```

## In-depth description of parameters

The `gitops` feature in the team concept have the same functionality as in the tenant concept´s `argocd` feature.

As in the tenant concept you also have the ability to enable the user-defined method and the auto-defined method.

1. **User-defined method:** Create ArgoCD applications in your repository under the path `<path>/<alle argocd apps generert her>`.
    - To enable this choice, you must set the field `gitops.argocd.enable_user_defined_apps` to true.

2. **Auto-defined method:** Use an ArgoCD applicationSet to create your applications automatically under the path `<path>/<alle folders her blir generert>`.
    
    - To enable this choice you have to set the  `gitops.argocd.enable_auto_defined_apps field` to true. This will create an ApplicationSet for the team namespace and will configure new applications when you add new folders in your team folder.

More information about how to set up a Git Repository for ArgoCD on OpenShift can be found here:

* [OpenShift GitOps - Introduction](../../OpenShift%20GitOps/Introduction-GitOps.md) 

### Connecting to a Git repository
The `argocd` feature can connect to a Git repository through a Personal Access Token (PAT), a GitHub App or SSH. The table below shows a more detailed description of each variable in the `argocd` feature under the `main_git_repository`. The table is split into three categories: 

- **Default**: variables that both connection methods need
- **PAT**: variables needed to connect with a GitHub PAT token
- **GitHub app**: variables need to configure the GitHub app
- **SSH**: variables need to configure through SSH

| <div style="width:140px">**Variable**</div>         | **Description**                                                                                                     | **Example**                                | **Type**                  | **Default Value**  |
|----------------------|---------------------------------------------------------------------------------------------------------------------|--------------------------------------------|---------------------------|------------|
| SyncPolicy              |                                                                                                                     |                                            |                           |
| `allowEmpty`            | Allows ArgoCD to sync an ApplicationSet even if it results in an empty application                               | True / False | Boolean                    | true |
| `selfHeal`            | Automatically repair out-of-sync resources to match the desired state in Git                                        | True / False | Boolean                    | true |
| `prune`           | Remove resources that are not present in the Git repository during sync                                                | True / False                                 | Boolean                    | true |
| Argo CD Configuration              |                                                                                                                     |                                            |                           |
| `repourl`            | The URL of the git repository which ArgoCD will use as its "source of truth"                                        | https://github.com/customer-repo/openshift | String                    | "" |
| `encrypted_url`      | The URL of the git repository encrypted with sealedsecrets                                                          | See description below                      | Kubeseal encrypted String | "" |
| `encrypted_type`     | Type should always be "git" and encrypted with sealedsecrets                                                        | See description below                      | Kubeseal encrypted String | "" |
| 
| GitHub App           |                                                                                                                     |                                            |                           |
| `enable_app`         | Whether or not to use GitHub App to authtenticate ArgoCD with your Git Repository. Default false.                   | True / False                               | Boolean                   | false |
| `id`                 | The app id for your Github App encrypted with sealedsecrets                                                         | See description below                      | Kubeseal encrypted String | "" |
| `installation_id`    | The installation id for your GitHub App encrypted with sealedsecrets                                                | See description below                      | Kubeseal encrypted String | "" |
| `private_key`        | Private key for your GitHub App encrypted with sealedsecrets                                                        | See description below                      | Kubeseal encrypted String | "" |
| SSH           |                                                                                                                     |                                            |                           |
| `enable_ssh_key`         | Whether or not to use SSH to authtenticate ArgoCD with your Git Repository. Default false.                   | True / False                               | Boolean                   | false |
| `private_key`        | Private key for your SSH-private-key encrypted with sealedsecrets                                                        | See description below                      | Kubeseal encrypted String | "" |
PAT                  |                                                                                                                     |                                            |                           |
| `username` | The username of the service account connecting to the git repository encrypted with sealedsecrets                   | See description below                      | Kubeseal encrypted String | "" |
| `password` | The git Personal Access Token for the service account connecting to the git repository encrypted with sealedsecrets | See description below                      | Kubeseal encrypted String | "" |


## Encrypt and configure the Argo-specific information

Encrypting and configuring ArgoCD-specific information is crucial for ensuring the security and efficiency of your deployment. To assist with this, we have developed a detailed user guide. 

This guide provides step-by-step instructions and best practices for encrypting Personal Access Tokens (PAT), GitHub App variables and SSH-private-key, which are essential for the secure operation of ArgoCD within your OpenShift environment. Please follow the steps in the guide provided below:
 
* [Authenticate with Personal Access Token (PAT)](../../OpenShift%20Tenants/Tenant%20features/GitOps/Authentication%20Methods%20for%20ArgoCD/authenticate-with-personal-access-token.md).
* [Authenticate with a GitHub App](../../OpenShift%20Tenants/Tenant%20features/GitOps/Authentication%20Methods%20for%20ArgoCD/authenticate-with-github-app.md).
* [Authenticate with SSH](../../OpenShift%20Tenants/Tenant%20features/GitOps/Authentication%20Methods%20for%20ArgoCD/authenticate-with-ssh.md).
