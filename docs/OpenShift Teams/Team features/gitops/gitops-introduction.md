# GitOps Introduction - Team Concept

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


