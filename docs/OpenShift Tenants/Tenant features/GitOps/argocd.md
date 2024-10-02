# ArgoCD

## What is ArgoCD?

To implement GitOps in our delivery pipeline, we use ArgoCD, a declarative, continuous delivery tool for Kubernetes and OpenShift. ArgoCD allows you to define the desired state of your applications in Git repositories and ensures that your OpenShift cluster matches this state. 

By continuously monitoring your applications, ArgoCD automatically synchronizes the live state to the desired state specified in Git, providing automated deployments and tracking of application versions. 

This seamless integration with GitOps practices ensures that our applications are always up-to-date and that any changes are managed through a robust version control system.

## How to configure Argo CD

 Below is the configuration for setting up ArgoCD using our Helm chart:

```yaml
...
  argocd: 
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
        enable_app: <Enable GitHub App to authenticate ArgoCD with your Git Repository. Default false.>
        id: <The app id for your GitHub App encrypted with sealedsecrets>
        installation_id: <The installation id for your GitHub App encrypted with sealedsecrets.>
        private_key: <Private key for your GitHub App encrypted with sealedsecrets.>
      ssh_key:
        enable_ssh_key: <Enable SSH to authenticate ArgoCD with your Git Repository. Default false.>
        private_key: <Private key for your SSH-private-key encrypted with sealedsecrets.>
...    
```

## In-depth description of parameters

The `argocd` feature contains elements to synchronise tenant applications and infrastructures with a Git repository to ensure Continuous Deployment (CD). This means that developers can define application and environment configurations in a Git repository, and ArgoCD ensures that the cluster matches the defined state, automatically deploying and updating applications as needed. 

ArgoCD provides different ways of automatically deploying and synchronising infrastructure in a cluster. When connecting your Git Repository to your tenant, you have two options for creating applications: 

1. **User-defined method:** Create ArgoCD applications in your repository under the path `<basepath>applications/<environments[].name>.`
    - To enable this choice, you must set the field `argocd.enable_user_defined_apps` to true.

2. **Auto-defined method:** Use an ArgoCD applicationSet to create your applications automatically under the path `<basepath>applicationsets/<environments[].name>`.  
    
    - To enable this choice you have to set the  `argocd.enable_auto_defined_apps field` to true. This will create an ApplicationSet for each of the tenants' environments which will configure new applications when you hadd new folders in your repository.

More information about how to set up a Git Repository for ArgoCD on OpenShift can be found here:

* [OpenShift GitOps - Introduction](/docs/OpenShift%20GitOps/Introduction-GitOps.md) 

### Connecting to a Git repository
The `argocd` feature can connect to a Git repository through a Personal Access Token (PAT), a GitHub App or SSH. The table below shows a more detailed description of each variable in the `argocd` feature under the `main_git_repository`. The table is split into three categories: 

- **Default**: variables that both connection methods need
- **PAT**: variables needed to connect with a GitHub PAT token
- **GitHub app**: variables need to configure the GitHub app
- **SSH**: variables need to configure through SSH

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
| SSH           |                                                                                                                     |                                            |                           |
| `enable_ssh_key`         | Whether or not to use SSH to authtenticate ArgoCD with your Git Repository. Default false.                   | True / False                               | Boolean                   |
| `private_key`        | Private key for your SSH-private-key encrypted with sealedsecrets                                                        | See description below                      | Kubeseal encrypted String |

### Encrypt and configure the Argo-specific information

Encrypting and configuring ArgoCD-specific information is crucial for ensuring the security and efficiency of your deployment. To assist with this, we have developed a detailed user guide. 

This guide provides step-by-step instructions and best practices for encrypting Personal Access Tokens (PAT), GitHub App variables and SSH-private-key, which are essential for the secure operation of ArgoCD within your OpenShift environment. Please follow the steps in the guide provided below:
 
* [Authenticate with Personal Access Token (PAT)](./Authentication%20Methods%20for%20ArgoCD%20with%20GitHub/authenticate-with-personal-access-token.md).
* [Authenticate with a GitHub App](./Authentication%20Methods%20for%20ArgoCD%20with%20GitHub/authenticate-with-github-app.md).
* [Authenticate with SSH](./Authentication%20Methods%20for%20ArgoCD%20with%20GitHub/authenticate-with-ssh.md).