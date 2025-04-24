---
Author: Rune Landa Gjerde
Title: Order OpenShift tenant team
Version: 1.0
externally-exposed: true
--- 
# Team

## What is a team in regards to tenants?

Team overlay will let you group multiple tenants within one team and ease the administration of tenants that is managed by the same development team. it allows creation of a team grafana for observability.
When team is created in openshift, a new namespace wit the team name will be created, in this namespace all common secrets and service accounts will be created. 

## How to configure team

Within the team definitions yaml file you can configure the following:


```yaml
team:
  name: <Name of team>
  description: <Description of team>

resource_management:
  requests:
    cpu: 200m
    memory: "500Mi"
  storage:
    enable_custom_storageclass: false

rbac:  
  team_admin: <AD Group for team admin>

observability:
  grafana_instance: false
  grafana_admin: <AD Group for grafana admin>
  grafana_editor: <AD Group for editors, can be left blank and everyone will be editor>
  sopra_grafana_admin: ""

gitops:
  gitops_namespace: gitops-developers
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
