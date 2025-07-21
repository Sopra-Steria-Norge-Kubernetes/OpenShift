---
Author: Rune Landa Gjerde
Title: Order OpenShift tenant team
Version: 1.0
externally-exposed: true
--- 
# Team Introduction

## What is a Team?

**Teams** are organizational units that enable centralized management of multiple related tenants. Teams provide shared resources and configurations that simplify administration for development teams managing multiple applications or microservices.

When a team is created in OpenShift, a dedicated namespace is established where all common secrets, service accounts, and shared resources are managed centrally.

## Team-Tenant Relationship

Teams act as a **management layer** above tenants:

Below is an illustration of teams.
![Team-Tenant Correlation](../img/Developer%20Teams/team-tenant-corraltion.png)

## Team Features

### **Centralized Resource Management**
- **Observability Dashboards**: Grafana instance with datasources for all team tenants
- **Secret Management**: ClusterSecretStores accessible by all team tenants  
- **GitOps Credentials**: Shared repository access credentials for ArgoCD

### **Benefits for Development Teams**
- **Reduced Duplication**: Share credentials and configurations across tenants
- **Unified Monitoring**: Single Grafana instance for all team applications
- **Simplified Administration**: Manage multiple tenants from one central location
- **Consistent Policies**: Apply team-level policies to all member tenants



## How to configure team

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

<!-- Moved to each feature under `<Team Features>` -->

<!-- # Features

```yaml
team:
  name: <name of team>
```
The team name chosen in team definitions value yaml is used in the tenant definitions yaml for connect tenants to the team. it is important that name is exactly the same.

```yaml
resource_management:
  requests:
    cpu: "200m"
    memory: "500Mi"
  storage:
    enable_custom_storageclass: false
```

Resource managment is used to set the cpu and memory request quotas for the team namespace.

```yaml
rbac:  
  team_admin: <AD Group for team admin>
```
rbac is used to give one specific group in openshift admin access over the team namespace. 

```yaml
observability:
  grafana_instance: false
  grafana_admin: <AD Group for grafana admin>
  grafana_editor: <AD Group for editors, can be left blank and everyone will be editor>
  notification:
    name: "teams-alert"
    type: teams <Tested with slack or teams, will most likely work with any webhook based type> 
    title: Grafana Alert
    webhook_url: "" <Slack or teams workflow webhook, will most likely work with other webhooks aswell>
    message: "An alert has been triggered in Grafana"
```
With observability you can enable a team grafana instance that uses the openshift credentials for login. This grafana instance will automaticly have datasources from all tenants managed by the team unless specificly disabled in the tenant definitions values yaml. grafana datasources from a tenant is default enabled. 

The grafana instance must have an admin group. it does not require editor group. but can be specified if multiple access levels is wanted. 

```yaml
gitops:
  argocd:
    enable_user_defined_apps: false
    enable_auto_defined_apps: true
    team_repo_url: ""
    path: ""
    syncPolicy:
      allowEmpty: true
      selfHeal: true
      prune: true
    resource_name_first: true
    custom_target_revision: false
  team_git_repositories:
  - repourl: "" 
    encrypted_type: ""
    encrypted_url: ""
    credentials:
      github_app: 
        enable_app: false
        id: ""
        installation_id: ""
        private_key: ""
      ssh_key:
        enable_ssh_key: false
        private_key: ""
      pat:
        enable_pat: false
        username: ""
        password: ""
```

The gitops feature is used to define the login credentials for Argo CD to use aswell as the repository Argo CD will use for the Argo CD applicationsets and applications.

You can read more in depth about this feature by following this link: [ArgoCD](../OpenShift%20Tenants/Tenant%20features/GitOps/argocd.md)

```yaml
secret_management:
  external_secrets:
    enable: false
    tenant_id: ""
    team_secretstores: 
    - name: ""
      keyvault_url: ""
      client_id: "" # namespace encrypted values
      client_secret: "" # namespace encrypted values
```

The secret managment feature is used to create global ClusterSecretStores in the team namespace, which can be utilized by multiple tenants that are a member of the team where the ClusterSecretStore is defined.

You can read more in depth about this feature by following this link: [Secret Managment](../OpenShift%20Tenants/Tenant%20features/external-secrets.md)
 -->
