---
Author: Eirik Opheim, Marcus Notø, Simen Haga
Title: Order OpenShift tenant
Version: 1.0.1
externally-exposed: true
--- 

# OpenShift Tenant Template 

To order an OpenShift tenant, you need to submit a values file to Sopra Steria through the designated ordering process. Based on the content of the submitted tenant form, different environments and resources will be created within an OpenShift cluster.

Below is all the currently possible parameters available for your `values.yaml` configuration file that you need to fill out with your specific tenant details:


``` yaml title="values.yaml"
appname: <Tenant Name>
values: |
  team:
    team_name: <team name>
    grafana_datasource: true
  namespace:
    name: <Tenant Name>
    description: <Tenant Description>
    displayName: <Display name of the tenant>
    use_egress_firewall: <Use egress firewall to limit egress traffic from tenant namespaces>
    enable_tooling: <Creates a tooling namespace>
    deploy_grafana: <Deploy Grafana instance in tooling namespace>
    limits:
      enable: true
      memory: <Memory limits of all tenants>
      cpu: <Combined CPU limits of all namespaces in tenant>
    requests:
      enable: false
      memory: <Memory requests of all tenants>
      cpu: <Combined CPU requests of all namespaces in tenant>
    container_limitrange:
      enable: true
       limits:
        memory: <Default Memory limitfor containers in all tenant namspace>  
        cpu: <Default cpu limit for containers in all tenant namspaces>
      requests:
        memory: <Default Memory request for containers in all tenant namspace>  
        cpu: <Default cpu request for containers in all tenant
    labels:
      custom_labels:
        <key>: <value>
        <key>: <value>

  environments:
    - name: <Name of environment 1 (e.g. dev)>
      allow_to_internet: <True or false - Set to true if environment should be exposed to internet>
      custom_auto_defined_targetRevision: <True or false - Set to true if targetRevision should be set by application folder name>
      externalURLs:
        - <URL's that should be reachable from the environment (e.g. google.com)>
      externalIPs:
        - <IP adresses that should be reachable from environment in CIDR format (e.g. 10.218.0.0/24)>

  networkPolicy:
    allow_same_namespace: true
    allow_from_openshift_monitoring: true
    allow_from_openshift_ingress: true
    allow_from_kube_apiserver_operator: true
    allow_from_grafana_operator: true
    allow_from_ECK_operator: true

  rbac:
    ad_group_write_access: <Azure AD group with write access>
    ad_group_read_access: <Azure AD group with read access>

  argocd: <See dedicated section for ArgoCD below>
    enable_user_defined_apps: <Enable creating applications with the user-defined method- app of apps (true/false)>
    enable_auto_defined_apps: <Enable using automatic application creation with an ArgoCD applicationsets per environment>
    syncPolicy:
      selfHeal: <Automatically repair out-of-sync resources to match the desired state in Git (true/false)>
      prune: <Remove resources that are not present in the Git repository during sync (true/false)>
    main_git_repository:
      repourl: 
      basepath:
      encrypted_url: <The url of the git repository encrypted with sealedsecrets>
      encrypted_type: <Type should always be git, but must encrypted with sealedsecrets>
      encrypted_password: <PAT encrypted with sealedsecrets>
      encrypted_username: <Username used with PAT encrypted with sealedsecrets>
      github_app: 
        enable_app: <Enable GitHub App to authenticate ArgoCD with your Git Repository. Default false.>
        id: <The app id for your GitHub App encrypted with sealedsecrets>
        installation_id: <The installation id for your GitHub App encrypted with sealedsecrets.>
        private_key: <Private key for your GitHub App encrypted with sealedsecrets.>
      ssh_key:
        enable_ssh_key: <Enable SSH to authenticate ArgoCD with your Git Repository. Default false.>
        private_key: <Private key for your SSH-private-key encrypted with sealedsecrets.>
      
  backup:
    backuplabel: <Label name you want to use for backups>
    take_backup: <Boolean if backup should be taken or not>
    schedules: 
      - schedule: <Schedule for backups to be taken in cron format (e.g. "30 * * * *")>
        name: <Name of schedule> 
        ttl: <Time until a backup is deleted in format XXXhYYmZZs e.g. 24h0m0s >

  external_secrets:
    azure_tenant_id: <Azure-Tenant-ID>
    keyvault_credentials:
      client_id: <Client-ID for Azure Key Vault (ServicePrinciple)>
      client_secret: <Client-Secret for Azure Key Vault (ServicePrinciple)>

  slack_alert_integration:
    enable: False
    alert_severity: critical # If multiple; critical|warning|info
    webhook_secret:
      encrypted_webhookURL: <webhook to slack channel encrypted with sealedsecrets>
    
```

Below is an example of all the currently possible parameters available for your `values.yaml` configuration file with ONLY the default values filled in:

``` yaml title="values.yaml"
appname: ""
values: |
  team:
    team_name: ""
    grafana_datasource: false
  namespace:
    name: ""
    description: ""
    displayName: ""
    use_egress_firewall: true
    enable_tooling: true
    deploy_grafana: true
    limits:
      enable: true
      memory: 1Gi
      cpu: 1
    requests:
      enable: false
      memory: 1Gi
      cpu: 1
    container_limitrange:
      enable: true
       limits:
        memory: "1Gi"
        cpu: "250m"
      requests:
        memory: ""  
        cpu: ""
    labels:
      custom_labels:
        <key>: <value>
        <key>: <value>

  environments:
    - name: ""
      allow_to_internet: false
      custom_auto_defined_targetRevision: false
      externalURLs:
        - 
      externalIPs:
        - 

  networkPolicy:
    allow_same_namespace: true
    allow_from_openshift_monitoring: true
    allow_from_openshift_ingress: true
    allow_from_kube_apiserver_operator: true
    allow_from_grafana_operator: true
    allow_from_ECK_operator: true

  rbac:
    ad_group_write_access: ""
    ad_group_read_access: ""

  argocd: 
    enable_user_defined_apps: false
    enable_auto_defined_apps: true
    syncPolicy:
      selfHeal: true
      prune: true
    main_git_repository:
      repourl: ""
      basepath: ""
      encrypted_url: ""
      encrypted_type: ""
      encrypted_password: ""
      encrypted_username: ""
      github_app: ""
        enable_app: false
        id: ""
        installation_id: 
        private_key: ""
      ssh_key:
        enable_ssh_key: false
        private_key: ""
      
  backup:
    backuplabel: ""
    take_backup: false
    schedules: 
      - schedule: ""
        name: "" 
        ttl: ""

  external_secrets:
    azure_tenant_id: ""
    keyvault_credentials:
      client_id: ""
      client_secret: ""

  slack_alert_integration:
    enable: false
    alert_severity: ""
    webhook_secret:
      encrypted_webhookURL: ""
    
```

To read more about all the Tenant Features available go to the section: 

- [Tenant Features](../OpenShift%20Tenants/Tenant%20features/landingpage.md)
