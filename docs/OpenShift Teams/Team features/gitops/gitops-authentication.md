## GitOps - Authentication & Credentials

On this page you can find information related to the `gitops.authentication` feature in the team concept. This page contains an example of how to implement the credentials Argo CD will utilize to authenticate towards a repository

## How to Configure authentication credentials

Below we will go through an example on how to implemet the `gitops.authentication` feature in the team chart using GitHub App credentials as the authentication method. We will show how this can be done by using either external secrets or sealedsecrets.

!!! warning
    To use external secrets for defining authentication methodes, you need to have set up a ClusterSecretStore in your team to pull down the credentials from your Key Vault provider.

    How to set up a ClusterSecretStore: [Secret Managment](../secret-management.md)

Below is an example creating an external secret as an authentication method for Argo CD with a GitHub APP:

```yaml
gitops:
  authentication:
    external_secrets:
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
    sealed_secrets:
      github_app: 
      - id: ngwio847359JHUjigiIIG98796HJ7697gug898GiuG... # SealedSecret
        installation_id: biUYGVUVh786758GU78gUYGujad78hjJ... # SealedSecret
        private_key: dhvibibvwiIYFHUKBSBIOH&ABCGFVW895487u... # SealedSecret
        type: IBKhwofi8979jBHJv78gUi8011IIuhfew98... # SealedSecret
        repo_url: BIbi8473rege786JKHhgj8BhdksuV78Jl... # SealedSecret
```

Below is all possible fields to configure for the `gitops.authentication` feature

```yaml
gitops:
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

## Connecting to a Git repository
The `argocd` feature can connect to a Git repository through a Personal Access Token (PAT), a GitHub App or SSH. The table below shows a more detailed description of each variable in the `gitops.argocd` feature under `authentication`. The table is split into four categories: 

- **helm_registry**: variables needed to connect with a remote helm registry
- **PAT**: variables needed to connect with a GitHub PAT token
- **GitHub app**: variables need to configure the GitHub app
- **SSH**: variables need to configure through SSH

| <div style="width:140px">**Variable**</div>            | **Description**                                                                                                     | **Example**                                | **Type**                  | **Default Value**  |
|----------------------|---------------------------------------------------------------------------------------------------------------------|--------------------------------------------|---------------------------|------------|
|  Argo CD Authentication             |                                                                                                                            |                                            |                           |
| Helm Registry           |                                                                                                                     |                                            |        list[]
| `username`                 | ACR username - Azure Key Vault secret name or encrypted with sealedsecrets                                                      | See description below                      | String / Kubeseal Encrypted String | "" |
| `password`                 | ACR password - Azure Key Vault secret name or encrypted with sealedsecrets                                              | See description below                      | String / Kubeseal Encrypted String | "" |
| `registry_url`             | The url of the helm registry - Clear text or sealedsecret depending on the method                  |               ""                 | String / Kubeseal Encrypted String                  | "" |
| GitHub App           |                                                                                                                     |                                            |       list[] 
| `id`                 | The app id for your Github App - Clear text or sealedsecret depending on the method                                                      | See description below                      | String / Kubeseal Encrypted String | "" |
| `installation_id`    | The installation id for your GitHub App - Clear text or sealedsecret depending on the method                                              | See description below                      | String / Kubeseal Encrypted String | "" |
| `private_key`        | Private key for your GitHub App - Azure Key Vault secret name or encrypted with sealedsecrets                                                        | See description below                      | String / Kubeseal Encrypted String | "" |
| `repo_url`         | The url of the git repository - Clear text or sealedsecret depending on the method                  |               ""                 | String / Kubeseal Encrypted String                  | "" |
| SSH           |                                                                                                                     |                                            |           list[]                |
| `private_key`        | Private key for your SSH-private-key - Azure Key Vault secret name or encrypted with sealedsecrets                                                        | See description below                      | String / Kubeseal encrypted String | "" |
| `repo_url`         | The url of the git repository - Clear text or sealedsecret depending on the method                  |               ""                 | String / Kubeseal Encrypted String                  | "" |
PAT                  |                                                                                                                     |                                            |        list[]                   |
| `username` | The username of the service account connecting to the git repository - Azure Key Vault secret name or encrypted with sealedsecrets                  | See description below                      | String / Kubeseal encrypted String | "" |
| `password` | The git Personal Access Token for the service account connecting to the git repository - Azure Key Vault secret name or encrypted with sealedsecrets | See description below                      | String / Kubeseal encrypted String | "" |
| `repo_url`         | The url of the git repository - Clear text or sealedsecret depending on the method                  |               ""                 | String / Kubeseal Encrypted String                  | "" |
SealedSecrets Method Specific parameters                  |                                                                                                                     |                                            |                           |
| `type`     | Type should either be "git" or "helm" depending the authentication section - Encrypted with sealedsecrets                                                        | See description below                      | Kubeseal encrypted String | "" |  