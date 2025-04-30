# GitOps - Argo CD Feature

The gitops feature is used to define the login credentials for Argo CD to use aswell as the repository and path Argo CD will use for the Argo CD applicationsets and applications.

You can read more in depth about this feature by following this link: [ArgoCD](../../OpenShift%20Tenants/Tenant%20features/GitOps/argocd.md)

## How to configure Argo CD - Team Concept

Below is the configuration for setting up ArgoCD using our team concept:

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

### Example - Configure ArgoCD Applicationset with GitHub App Credentials

Below we will go through an example on how to implemet the gitops feature in the team chart using GitHub App credentials as the authentication method for Argo CD. The example utilizes the default values for `SyncPolicies`, `resource_name_first`, and `custom_target_revision`. We will show how this can be done by using either external secrets or sealedsecrets.

!!! Info
    To use external secrets for defining authentication methodes, you need to have set up a ClusterSecretStore in your team to pull down the credentials from your Azure Key Vault.

    How to set up a ClusterSecretStore: [Secret Managment](./secret-management.md)


Below is an example of how the `gitops.argocd` section for defining applicationset details can look like:

```yaml
gitops:
  argocd:
    enable_auto_defined_apps: true
    team_repo_url: https://github.com/customer-repo/openshift
    path: "/path/to/applications"

```

Below is an example creating an external secret as an authentication method for Argo CD with a GitHub APP:

```yaml
gitops:
  authentication:
    external_secret:
      secretstore: gitops
      github_app:
      - id: 374237872
        installation_id: 8947359869
        private_key: GithubAppPrivateKey
        repo_url: https://github.com/customer-repo/openshift
```

Below is an example using kubeseal to create a sealedsecret as an authentication method for Argo CD with a GitHub APP:

```yaml
gitops:
  authentication:
    sealed_secret:
      github_app: 
      - id: ngwio847359JHUjigiIIG98796HJ7697gug898GiuG... # SealedSecret
        installation_id: biUYGVUVh786758GU78gUYGujad78hjJ... # SealedSecret
        private_key: dhvibibvwiIYFHUKBSBIOH&ABCGFVW895487u... # SealedSecret
        type: IBKhwofi8979jBHJv78gUi8011IIuhfew98... # SealedSecret
        repo_url: BIbi8473rege786JKHhgj8BhdksuV78Jl... # SealedSecret
```

Below is a combined example of how to configuration ArgoCD Applicationset with GitHub App Credentials using external secrets:

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
|  Argo CD Authentication             |                                                                                                                            |                                            |                           |
| `repourl`            | The URL of the git repository which ArgoCD will create credentials for                                     | https://github.com/customer-repo/openshift | String                    | "" |
| `encrypted_url`      | The URL of the git repository encrypted with sealedsecrets                                                          | See description below                      | Kubeseal encrypted String | "" |
| `encrypted_type`     | Type should always be "git" and encrypted with sealedsecrets                                                        | See description below                      | Kubeseal encrypted String | "" |   
| GitHub App           |                                                                                                                     |                                            |        
| `id`                 | The app id for your Github App - Clear text or sealedsecret depending on the method                                                      | See description below                      | String / Kubeseal Encrypted String | "" |
| `installation_id`    | The installation id for your GitHub App - Clear text or sealedsecret depending on the method                                              | See description below                      | String / Kubeseal Encrypted String | "" |
| `private_key`        | Private key for your GitHub App - Azure Key Vault name or encrypted with sealedsecrets                                                        | See description below                      | String / Kubeseal Encrypted String | "" |
| `repo_url`         | The url of the git repository - Clear text or sealedsecret depending on the method                  |               ""                 | String / Kubeseal Encrypted String                  | "" |
| SSH           |                                                                                                                     |                                            |                           |
| `private_key`        | Private key for your SSH-private-key - Azure Key Vault name or encrypted with sealedsecrets                                                        | See description below                      | String / Kubeseal encrypted String | "" |
| `repo_url`         | The url of the git repository - Clear text or sealedsecret depending on the method                  |               ""                 | String / Kubeseal Encrypted String                  | "" |
PAT                  |                                                                                                                     |                                            |                           |
| `username` | The username of the service account connecting to the git repository - Azure Key Vault name or encrypted with sealedsecrets                  | See description below                      | String / Kubeseal encrypted String | "" |
| `password` | The git Personal Access Token for the service account connecting to the git repository - Azure Key Vault name or encrypted with sealedsecrets | See description below                      | String / Kubeseal encrypted String | "" |
| `repo_url`         | The url of the git repository - Clear text or sealedsecret depending on the method                  |               ""                 | String / Kubeseal Encrypted String                  | "" |


## Encrypt and configure the Argo-specific information

Encrypting and configuring ArgoCD-specific information is crucial for ensuring the security and efficiency of your deployment. To assist with this, we have developed a detailed user guide. 

This guide provides step-by-step instructions and best practices for encrypting Personal Access Tokens (PAT), GitHub App variables and SSH-private-key, which are essential for the secure operation of ArgoCD within your OpenShift environment. Please follow the steps in the guide provided below:
 
* [Authenticate with Personal Access Token (PAT)](../../OpenShift%20Tenants/Tenant%20features/GitOps/Authentication%20Methods%20for%20ArgoCD/authenticate-with-personal-access-token.md).
* [Authenticate with a GitHub App](../../OpenShift%20Tenants/Tenant%20features/GitOps/Authentication%20Methods%20for%20ArgoCD/authenticate-with-github-app.md).
* [Authenticate with SSH](../../OpenShift%20Tenants/Tenant%20features/GitOps/Authentication%20Methods%20for%20ArgoCD/authenticate-with-ssh.md).
