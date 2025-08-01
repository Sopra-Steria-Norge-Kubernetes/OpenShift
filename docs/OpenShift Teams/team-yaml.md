---
Author: Rune Landa Gjerde
Title: Order OpenShift tenant team
Version: 1.0
externally-exposed: true
--- 

# How to configure team

Within the team definitions yaml file you can configure the following:

```yaml
team:
  name: <Name of team>
  description: <Description of team>

resource_management: <Defualt resources allowed in the team namespaces for running Grafana, External Secret and GitOps>
  requests:
    cpu: <Default value is set to 200m>
    memory: <Default value is set to "500Mi">
  storage:
    enable_custom_storageclass: false 

rbac:  
  team_admin: <AD Group for team admin>

observability:
  grafana_instance: <Enable grafana instance in team namespace (true/false). default false>  
  grafana_admin: <AD Group for grafana admin>
  grafana_editor: <AD Group for editors, can be left blank and everyone will be editor>

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
    external_secrets:
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
    sealed_secrets:
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

secret_management:
  external_secrets:
    cluster_secret_stores: 
    - name: <ClusterSecretStore name. Team name will be prefix and then this name>
      tenant_id: <AZURE_TENANT_ID - Tenant ID of your organizations Azure tenant>
      keyvault_url: <Url to Azure Key Vault - https://AZURE_KEY_VAULT_URL> 
      client_id: <Sealed Secret App Registration Credentials - SealedSecret_CLIENT_ID> 
      client_secret: <Sealed Secret - App Registration Credentials -SealedSecret_CLIENT_SECRET> 
```