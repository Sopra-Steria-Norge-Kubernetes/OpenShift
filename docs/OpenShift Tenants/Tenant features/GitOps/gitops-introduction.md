# GitOps & ArgoCD Introduction

## What is ArgoCD?

To implement GitOps in our delivery pipeline, we use ArgoCD, a declarative, continuous delivery tool for Kubernetes and OpenShift. ArgoCD allows you to define the desired state of your applications in Git repositories and ensures that your OpenShift cluster matches this state. 

This seamless integration with GitOps practices ensures that our applications are always up-to-date and that any changes are managed through a robust version control system.

## How to configure Argo CD - Tenant Concept

Below is the configuration for setting up ArgoCD using our tenant concept:

```yaml
...
  argocd: 
    enable_user_defined_apps: <Enable creating applications with the user-defined method- app of apps (true/false)>
    enable_auto_defined_apps: <Enable using automatic application creation with an ArgoCD applicationsets per environment>
    syncPolicy:
      allowEmpty: <Allows ArgoCD to sync an ApplicationSet even if it results in an empty application (true/false)>
      selfHeal: <Automatically repair out-of-sync resources to match the desired state in Git (true/false)>
      prune: <Remove resources that are not present in the Git repository during sync (true/false)>
    main_git_repository:
      repourl: 
      path:
...    
```

## In-depth description of parameters

ArgoCD provides different ways of automatically deploying and synchronising infrastructure in a cluster. When connecting your Git Repository to your tenant, you have two options for creating applications: 

1. **User-defined method:** Create ArgoCD applications in your repository under the path `<path>/application/<miljø>/<alle argocd apps generert her>`.
    - To enable this choice, you must set the field `argocd.enable_user_defined_apps` to true.

2. **Auto-defined method:** Use an ArgoCD applicationSet to create your applications automatically under the path `<path>/applicationset/<miljø>/<alle folders her blir generert>`.  
    
    - To enable this choice you have to set the  `argocd.enable_auto_defined_apps field` to true. This will create an ApplicationSet for each of the tenants' environments which will configure new applications when you hadd new folders in your repository.

### Connecting to a Git repository
The `argocd` feature can connect to a Git repository through a Personal Access Token (PAT), a GitHub App or SSH. How to configure the authentication to a Git repository can be seen [**here**](./gitops-authentication.md) 


<!-- | <div style="width:140px">**Variable**</div>         | **Description**                                                                                                     | **Example**                                | **Type**                  | **Default Value**  |
|----------------------|---------------------------------------------------------------------------------------------------------------------|--------------------------------------------|---------------------------|------------|
| SyncPolicy              |                                                                                                                     |                                            |                           |
| `allowEmpty`            | Allows ArgoCD to sync an ApplicationSet even if it results in an empty application                               | True / False | Boolean                    | true |
| `selfHeal`            | Automatically repair out-of-sync resources to match the desired state in Git                                        | True / False | Boolean                    | true |
| `prune`           | Remove resources that are not present in the Git repository during sync                                                | True / False                                 | Boolean                    | true |
| Default              |                                                                                                                     |                                            |                           |
| `repourl`            | The URL of the git repository which ArgoCD will use as its "source of truth"                                        | https://github.com/customer-repo/openshift | String                    | "" |
| `basepath`           | The basepath of the git repository where ArgoCD manifests are stored                                                | poseidon1/                                 | String                    | "" | -->

<!-- 
## Encrypt and configure the Argo-specific information

This guide provides step-by-step instructions and best practices for encrypting Personal Access Tokens (PAT), GitHub App variables and SSH-private-key, which are essential for the secure operation of ArgoCD within your OpenShift environment. Please follow the steps in the guide provided below:
 
* [Authentication Methods for ArgoCD](./gitops-authentication.md). -->

<!-- # GitOps Introduction - Team Concept

The `gitops` feature contains two main sections: `argocd` & `authentication`. The `authentication` section i used to define the authentication credential utilized by Argo CD. The `argocd` section is used to define the repository Argo CD will connect to and the path where Argo CD will search for applications & applicationsets 

You can read more in depth about this feature by following this link: [ArgoCD](../../../OpenShift%20Tenants/Tenant%20features/GitOps/argocd.md)

On this page we will go through a simple example to get started with the gitops feature in addition to showing all possible fields and give an in depth description of each of the fields

## Get Started

To get started with using the team concept´s GitOps feature you need to have your repository url and your login credentials availible. This can be in the form of a GitHub App, SSH private key or PAT token. 

!!! warning
    To use external secrets for defining authentication methodes, you need to have set up a ClusterSecretStore in your team to pull down the credentials from your Key Vault provider.

    How to set up a ClusterSecretStore: [Secret Managment](../secret-management.md)

Below is a simple example of how you can configuration ArgoCD Applicationset with GitHub App Credentials using external secrets:

```yaml
gitops:
  argocd:
    enable_auto_defined_apps: true
    team_repo_url: https://github.com/customer-repo/openshift
    path: "/path/to/applications"
  authentication:
    external_secret:
      secretstore: gitops
      github_app:
      - id: 374237872
        installation_id: 8947359869
        private_key: GithubAppPrivateKey
        repo_url: https://github.com/customer-repo/openshift
```

## GitOps configuration - Team Concept

Below is all the possible configuration fields avalible to modify in the gitops feature:

```yaml
gitops:
  argocd:
    enable_user_defined_apps: <Enable creating applications with the user-defined method- app of apps (true/false). Defualt false>
    team_repo_url:  <Git repository url that GitOps (ArgoCD) will use as its "source of truth" for the team namespace> 
    path: <Path to the folder that contains infrastructure that the applicationsets will insert into the team namespace>
    syncPolicy:
      allowEmpty: <Allows ArgoCD to sync an ApplicationSet even if it results in an empty application (true/false). Default true>
      selfHeal: <Automatically repair out-of-sync resources to match the desired state in Git (true/false). Default true>
      prune: <Remove resources that are not present in the Git repository during sync (true/false). Default true>
    resource_name_first: <Nameingstandard for ArgoCD applications created by applicationSets. If true the name of the resource (folder) will come first if false then the name of the team will come first. Default true>
    custom_target_revision: <Allows setting the targetRevision at the application level for different environments in OpenShift. The generator picks up component names and creates targetRevision values based on the application folder name instead of using HEAD if set to true. Default false>
  authentication:
    external_secret:
      secretstore: <Name of SecretStore that contains all credentials for different authentication methods>
      helm_registry:
      - username: <Name of Azure Secret in Azure Key Vault>
        password: <Name of Azure Secret in Azure Key Vault>
        registry_url: <ACR login server url>
      github_app:
      - id: <The app id for your GitHub App>
        installation_id: <The installation id for your GitHub App>
        private_key: <Name of Azure Secret in Azure Key Vault>
        repo_url: <The url of the git repository>
      ssh_key:
      - private_key: <Name of Azure Secret in Azure Key Vault>
        repo_url: <The url of the git repository>
      pat:
      - username: <Name of Azure Secret in Azure Key Vault>
        password: <Name of Azure Secret in Azure Key Vault>
        repo_url: <The url of the git repository>
    sealed_secret:
      helm_registry:
      - username: <ACR username encrypted with sealedsecret>
        password: <ACR access token encrypted with sealedsecret>
        enableOCI: ""
        type: ""
        registry_url: <ACR login server url encrypted with sealedsecret>
      github_app: 
      - id: <The app id for your GitHub App encrypted with sealedsecrets>
        installation_id: <The installation id for your GitHub App encrypted with sealedsecrets>
        private_key: <Private key for your GitHub App encrypted with sealedsecrets>
        type: <Type should always be git, but must encrypted with sealedsecrets>
        repo_url: <The url of the git repository encrypted with sealedsecrets>
      ssh_key:
      - private_key: <Private key for your SSH-private-key encrypted with sealedsecrets>
        type: <Type should always be git, but must encrypted with sealedsecrets>
        repo_url: <The url of the git repository encrypted with sealedsecrets>
      pat:
      - username: <Username used with PAT encrypted with sealedsecrets>
        password: <PAT encrypted with sealedsecrets>
        type: <Type should always be git, but must encrypted with sealedsecrets>
        repo_url: <The url of the git repository encrypted with sealedsecrets>
```

 -->
