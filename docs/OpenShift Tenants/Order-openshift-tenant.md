---
Author: Eirik Opheim, Marcus Notø, Simen Haga
Title: Order OpenShift tenant
Version: 1.0.1
externally-exposed: true
--- 

# Order OpenShift tenant
This user guide describes the process of gathering and presenting the information that Sopra Steria needs to create a new OpenShift tenant. 
An Openshift Tenant form is added to a collective Git repository and shared with Sopra Sterias OpenShift clusters. Different environments and resources are created in an OpenShift Cluster based on the content of the created tenant form.

## Pre-requisites
Before getting started with this guide, ensure you have the following prerequisites:

- Create Azure AD groups and connect them and the Azure tenant with an application registration towards the Openshift Cluster. 
- Have a Git repository with Personal Access Tokens (PAT) or a GitHub Application. 

## OpenShift Tenant Template

In this guide, we will fill out the following form:

``` yaml
appname: <Tenant Name>
values: |
  namespace:
    name: <Tenant Name>
    description: <Tenant Description>
    displayName: <Display name of the tenant>
    use_egress_firewall: <Use egress firewall to limit egress traffic from tenant namespaces>
    enable_tooling: <Creates a tooling namespace>
    deploy_grafana: <Deploy Grafana instance in tooling namespace>
    limits:
      memory: <Memory limits of all tenants>
      cpu: <Combined CPU limits of all namespaces in tenant>

  environments:
    - name: <Name of environment 1 (e.g. dev)>
      externalURLs:
        - <URL's that should be reachable from the environment (e.g. google.com)>
      externalIPs:
        - <IP adresses that should be reachable from environment in CIDR format (e.g. 10.218.0.0/24)>

  networkPolicy:
    allow_same_namespace: true
    allow_from_openshift_monitoring: true
    allow_from_openshift_ingress: true
    allow_from_kube_apiserver_operator: true

  rbac:
    ad_group_write_access: <Azure AD group with write access>
    ad_group_read_access: <Azure AD group with read access>

  argocd: <See dedicated section for ArgoCD below>
    enable_user_defined_apps: <Enable creating applications with the user-defined method- app of apps (true/false)>
    enable_auto_defined_apps: <Enable using automatic application creation with an ArgoCD applicationsets per environment>
    main_git_repository:
      repourl: 
      basepath:
      encrypted_url: <The url of the git repository encrypted with sealedsecrets>
      encrypted_type: <Type should always be git, but must encrypted with sealedsecrets>
      encrypted_password: <PAT encrypted with sealedsecrets>
      encrypted_username: <Username used with PAT encrypted with sealedsecrets>
      github_app: 
        enable_app: < Enable GitHub App to authenticate ArgoCD with your Git Repository. Default false.>
        id: <The app id for your GitHub App encrypted with sealedsecrets>
        installation_id: <The installation id for your GitHub App encrypted with sealedsecrets.>
        private_key: <Private key for your GitHub App encrypted with sealedsecrets.>
      
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
    channel_name: <The name of the slack channel to receive alerts>
    alert_severity: critical # If multiple; critical|warning|info
    webhook_secret:
      encrypted_webhookURL: <webhook to slack channel encrypted with sealedsecrets>
    
```
The values file for your Tenant needs to be sent to Sopra Steria through your defined ordering process.


## Detailed Explanation of each Tenant feature and variable:

Below is more detailed information about tenant features and variables under the `values` variable.


### Namespace

The `namespace` feature contains information about Tenant namespaces and Tenant resources. In the table below, you can find a more detailed description of each variable in the `namespace` feature:


| <div style="width:205px">**Variable**</div>           | **Description**                                                                      | **Example**                                | **Type**   |
|:------------------------------|---------------------------------------------------------------------------------------|--------------------------------------------|:------------|
| `name`                         | Base name of tenant.                                                                 | Poseidon1                                  | String     |
| `description`                | A description annotation  under each tenant namespace                                 | " This is a test tenant used for testing" | String     |
| `displayName`                | Displayname given to each openshift namespace/project                                 | "poseidon1-application1"                   | String     |
| `use_egress_firewall`         | To use egress firewall to limit egress traffic from tenant namespaces.                | true                                       | Boolean    |
| `enable_tooling`              | Creates a tooling namespace which is needed for certain applications such as Grafana | true                                       | Boolean    |
| `deploy_grafana`              | Deploys a grafana instance in the tooling namespace                                      | true                                       | Boolean    |
| `limits.memory`               | Combined memory limit for all tenant namespaces. The memory resource is measured in bytes. Memory can be expressed as a plain or fixed-point integer with one of these suffixes: E, P, T, G, M, K, Ei, Pi, Ti, Gi, Mi, Ki.    | 1Gi                                        | String/Int |
| `limits.cpu`                 | Combined cpu limit for all tenant namespaces. Fractional values are allowed. A Container that requests 500m CPU is guaranteed half as much CPU as a Container that requests 1 CPU. You can use the suffix m to mean milli. For example 100m CPU, 100 milliCPU, and 0.1 CPU are all the same. A precision finer than 1m is not allowed.                                         | 1                                          | String/Int |
| `container_limitrange.memory` | Memory limit for each container for all tenant namespaces.  The memory resource is measured in bytes. Memory can be expressed as a plain or fixed-point integer with one of these suffixes: E, P, T, G, M, K, Ei, Pi, Ti, Gi, Mi, Ki.                            | 64Mi                                       | String/Int |
| `container_limitrange.cpu`     | CPU limit for each container for all tenant namespaces. Fractional values are allowed. A Container that requests 0.5 CPU is guaranteed half as much CPU as a Container that requests 1 CPU. You can use the suffix m to mean milli. For example 100m CPU, 100 milliCPU, and 0.1 CPU are all the same. A precision finer than 1m is not allowed.                                  | 100m                                       | String/Int |

### Environment
The `environment` feature contains a list of namespaces and namespace-specific variables.
In the table below, you can find a more detailed description of each variable in the `environment` feature:

|  <div style="width:205px">**Variable**</div>                | **Description**                                                        | **Example**                | **Type** |
|------------------------------|------------------------------------------------------------------------|----------------------------|----------|
| `environments[].name`          | Name of environment                                                    | test                       | String   |
|  `environments[].externalURLs` | A list of URLs that should be reached from a tenant environment | [ testurl.com, google.com] | List     |
| `environments[].externalIPs`    | A list of IP ranges that should be reached from the tenant environment | [0.0.0.0/0, 92.0.2.1/24]   | List     |


### Network Policy

The `networkpolicy` is a feature which controls how pods in an openshift cluster can communicate with each other. These policies define rules for network traffic, specifying what is allowed and denied. Each sub-component should have either a `true` or `false` value. The default value is set to `true` for each sub-component. 
In the table below, you can find a more detailed description of each variable in the `networkpolicy` feature:

| <div style="width:255px">**Variable**</div>                                 | **Description**                                           | **Example**                 | **Type**  |
|-----------------------------------------------|-----------------------------------------------------------|-----------------------------|-----------|
| `allow_same_namespace`          | Allows or disallows network communication within the same namespace | true        | Boolean   |
| `allow_from_openshift_monitoring` | Allows or disallows network communication to OpenShift monitoring | true         | Boolean   |
| `allow_from_openshift_ingress`  | Allows or disallows incoming network traffic from OpenShift Ingress controllers | true        | Boolean   |
| `allow_from_kube_apiserver_operator` | Allows or disallows communication to Kubernetes API Server Operator. Allowing communication from the operator ensures proper management and maintenance of the API server. | false         | Boolean   |
| `allow_from_grafana_operator`    | Allows or disallows communication to Grafana Operator      | true      | Boolean   |


### RBAC

The `rbac` feature defines Role-Based Access Control (RBAC) settings for your OpenShift tenant.  It includes the following variables:

| **Variable**                  | **Description**                                                  | **Example**                    | **Type**  |
|-------------------------------|------------------------------------------------------------------|--------------------------------|-----------|
| `ad_group_write_access`       | Azure AD group given admin access to the tenant                  | AD-poseidon1-write        | String    |
| `ad_group_read_access`        | Azure AD group given read-only access to the tenant              | AD-poseidon1-read        | String    |



### ArgoCD - GitOps Desired State


The `argocd` feature contains elements to synchronise tenant applications and infrastructures with a Git repository to ensure Continuous Deployment (CD). This means that developers can define application and environment configurations in a Git repository, and ArgoCD ensures that the cluster matches the defined state, automatically deploying and updating applications as needed. 
ArgoCD provides different ways of automatically deploying and synchronising infrastructure in a cluster. When connecting your Git Repository to your tenant, you have two options for creating applications: 

1. User-defined method: Create ArgoCD applications in your repository under the path `<basepath>applications/<environments[].name>.`
   * To enable this choice, you must set the field `argocd.enable_user_defined_apps` to true.
2. Auto-defined method: Use an ArgoCD applicationSet to create your applications automatically under the path `<basepath>applicationsets/<environments[].name>`.  
   * To enable this choice you have to set the  `argocd.enable_auto_defined_apps field` to true. This will create an ApplicationSet for each of the tenants' environments which will configure new applications when you hadd new folders in your repository.

More information about how to set up a Git Repository for ArgoCD on OpenShift can be found here:

* [GitHub CaaS-GitOps](https://github.com/Sopra-Steria-Norge-Kubernetes/CaaS-GitOps) 
<!--
#Comment out this:
* [Configuring Applications Within The Tenant Concept Guide](/SolidCloud/SolidCloud-Products/Containers/Red-Hat-OpenShift/User-Guides/1%2DOpenShift-Tenants/1.3%2DConfiguring-Applications-Within-The-Tenant-Concept).
-->
#### Connecting to a Git repository
The `argocd` feature can connect to a Git repository through a Personal Access Token (PAT) or a GitHub App. The table below shows a more detailed description of each variable in the `argocd` feature under the `main_git_repository`. The table is split into three categories: 

- **Default**: variables that both connection methods need
- **PAT**: variables needed to connect with a GitHub PAT token
- **GitHub app**: variables need to configure the GitHub app

| <div style="width:140px">**Variable**</div>         | **Description**                                                                                                     | **Example**                                | **Type**                  |
|----------------------|---------------------------------------------------------------------------------------------------------------------|--------------------------------------------|---------------------------|
| Default              |                                                                                                                     |                                            |                           |
| `repourl`            | The URL of the git repository which ArgoCD will use as its "source of truth"                                        | https://github.com/customer-repo/openshift | String                    |
| `basepath`           | The basepath of the git repository where ArgoCD manifests are stored                                                | poseidon1/                                 | String                    |
| `encrypted_url`      | The URL of the git repository encrypted with sealedsecrets                                                          | See description below                      | Kubeseal encrypted String |
| `encrypted_type`     | Type should always be "git" and encrypted with sealedsecrets                                                        | See description below                      | Kubeseal encrypted String |
| PAT                  |                                                                                                                     |                                            |                           |
| `encrypted_password` | The git Personal Access Token for the service account connecting to the git repository encrypted with sealedsecrets | See description below                      | Kubeseal encrypted String |
| `encrypted_username` | The username of the service account connecting to the git repository encrypted with sealedsecrets                   | See description below                      | Kubeseal encrypted String |
| GitHub App           |                                                                                                                     |                                            |                           |
| `enable_app`         | Whether or not to use GitHub App to authtenticate ArgoCD with your Git Repository. Default false.                   | True / False                               | Boolean                   |
| `id`                 | The app id for your Github App encrypted with sealedsecrets                                                         | See description below                      | Kubeseal encrypted String |
| `installation_id`    | The installation id for your GitHub App encrypted with sealedsecrets                                                | See description below                      | Kubeseal encrypted String |
| `private_key`        | Private key for your GitHub App encrypted with sealedsecrets                                                        | See description below                      | Kubeseal encrypted String |



#### Encrypt and configure the Argo-specific information

Encrypting and configuring ArgoCD-specific information is crucial for ensuring the security and efficiency of your deployment. To assist with this, we have developed a detailed user guide. This guide provides step-by-step instructions and best practices for encrypting Personal Access Tokens (PAT) and GitHub App variables, which are essential for the secure operation of ArgoCD within your OpenShift environment. Please follow the steps in the guide provided below:
 
* [Encrypt PAT and GitHub App variables for ArgoCD](Encrypt-PAT-and-GitHub-App-variables-for-argo-CD.md). 


### Backup


The `backup` feature allows you to create backup schedules for the data inside your Tenants' namespaces. In the table below, there is a more detailed description of each variable in the `backup` feature:


| <div style="width:155px">**Variable**</div>                     | **Description**                                           | **Example**                | **Type**  |
|----------------------------------|-----------------------------------------------------------|----------------------------|-----------|
| `backuplabel`             | The label to set for backups. If this label is not set, a backup of your applications will not be taken. It needs to be on the format `Key: value` | "backupresource: true"| String   |
| `take_backup`             | Used to specify whether backups should be taken or not in the Tenant.  | true     | Boolean   |
| `schedules[]`      | A list of backup schedules that will be implemented for the tenant |  | List    |
| `schedules[].schedule`      | The schedule at which backups should be taken in cron time format | "0 0 * * *"             | String    |
| `schedules[].name`          | The name of the backup schedule. It is prefixed with the name of the tenant. If the tenant name is `poseidon1` and this variable is set to `daily`, the backup schedule will be called `poseidon1-daily`                         | "daily"            | String    |
| `schedules[].ttl`       | The time until the backup is deleted. It is in the format of `XXXhYYmZZs`, where `XXX` is the number of hours, `YY` is the number of minutes, and `ZZ` is the number of seconds. For example, `24h0m0s` would indicate that the backup should be deleted after 24 hours.| "48h0m0s"                 | String    |



### External Secrets

The `external_secret` feature allows the tenant to use External Secrets in OpenShift, which allows applications to access sensitive data stored in a remote Key Management System (KMS). For more information about this feature, see the [Configuring External Secrets guide](/SolidCloud/SolidCloud-Products/Containers/Red-Hat-OpenShift/User-Guides/3%2DSecret-Management-on-OpenShift/3.3%2DConfiguring-External-Secrets).
This feature is only supported for Azure Key Vault, but contact your OpenShift administrator for additional KMS support.

In the table below, there is a more detailed description of each variable in the `backup` feature:


| <div style="width:255px">**Variable**</div>                         | **Description**                                  | **Example**                | **Type**  |
|--------------------------------------|--------------------------------------------------|----------------------------|-----------|
| `azure_tenant_id`                    | The Azure Tenant ID that your organisation uses for storing its Azure Key Vaults  |d93d3d23-50e3-46db-b3ad-8c6c281b431e      | String    |
| `keyvault_credentials.client_id`      | Username for Azure Key Vault (ServicePrinciple), which is encrypted as a sealed secret | | Kubeseal encrypted String    |
| `keyvault_credentials.client_secret`  | Password for Azure Key Vault (ServicePrinciple), which is encrypted as a sealed secret | | Kubeseal encrypted String    |


### Slack Alert Integration

The `slack_alert_integration` feature allows tenants to configure Slack notifications for their OpenShift environment. This integration uses an encrypted Slack webhook URL stored as a secret to send notifications to a Slack channel when an alert of specified severity is triggered.

Below is a detailed description of each variable in the `slack_alert_integration` feature:

| <div style="width:260px">**Variable**</div>                            | **Description**                                                                                  | **Example**                              | **Type**                   |
|-----------------------------------------|--------------------------------------------------------------------------------------------------|------------------------------------------|----------------------------|
| `enable`                                | Toggle the Slack alert integration feature. Set to `True` to enable.                             | `False`                                  | Boolean                    |
| `channel_name`                          | The Slack channel where alerts will be posted.                                                   | `developer-namespace`                            | String                     |
| `alert_severity`                        | The severity level of alerts to be sent. Multiple severities can be specified, separated by `|`. | `critical` or `critical|warning|info` | String                     |
| `webhook_secret.encrypted_webhookURL`   | The encrypted Slack webhook URL to be decrypted at runtime for sending alerts.                   | *Encrypted String*                       | Kubeseal encrypted String  |

For more information about configuring the Slack alert integration, refer to the [Integrating Slack Notifications guide](/SolidCloud/SolidCloud-Products/Containers/Red-Hat-OpenShift/User-Guides/3%2DSecret-Management-on-OpenShift/3.4%2DIntegrating-Slack-Notifications).
Ensure that the Slack webhook URL is encrypted using Kubeseal, providing a sealed secret which is Kubernetes' way of handling encrypted secrets.

