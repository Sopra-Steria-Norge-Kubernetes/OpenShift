# Configuring Argo CD Repository Secret with Your Team’s ClusterSecretStore

This guide explains how to use your team's configured ClusterSecretStore to deploy an Argo CD repository secret. This secret allows Argo CD to authenticate and fetch application code from source repositories or Helm registries.

!!! note
    For more details and advanced use cases, refer to our [extended documentation]().

### Prerequisites

Before you begin, make sure the following requirements are met:

- A ClusterSecretStore has been configured for your team. See [this guide]() for setup instructions.
- The necessary credentials have been added to team's Key Vault.

### Argo CD Repository Authentication Using ExternalSecrets

To authenticate Argo CD with source repositories or Helm registries, you must reference credentials stored in your team’s `ClusterSecretStore`. To reference the `ClusterSecretStore`, the name defined in the `gitops.authentication.external_scerts.secretstore` must match what you named the clustersecretstore under `secret_management.external_secrets.cluster_secret_stores.name`.

In the secret definition for 

Sensitive values (e.g. tokens, private keys) should be stored as secrets in Azure Key Vault. These are referenced by key name. Non-sensitive values like usernames or URLs are written directly as plain text.

=== "Example: GitHub App Authentication Using ClusterSecretStore"
    ```yaml
    secret_management:
      external_secrets:
        cluster_secret_stores: 
        - name: gitops
          tenant_id: 8f3c5b3a-12d4-4e9f-9b92-7f04d2c44abc
          keyvault_url: https://keyvault-team-poseidon-test.vault.azure.net/
          client_id: AgAlMksI3FZJmIl3McPD[...]
          client_secret: AgBsdfhaL1Aw0HbX[...]

    gitops:
      authentication:
        external_secrets:
          secretstore: gitops
          github_app:
          - id: 123456
            installation_id: 654321
            private_key: GitHub-App-private-key
            repo_url: https://github.com/soprasteria/team-poseidon
    ```

---

### GitHub App Authentication

To authenticate using a GitHub App, provide the GitHub App credentials in your configuration as shown below:


### GitHub App authentication

To authenticate with GitHub App, you are required to add your private key to the team's `ClusterSecretStore`. Fill in the template as follows:

=== "GitHub App"
    ```yaml
    gitops:
      authentication:
        external_secrets:
          secretstore: ""
          github_app:
          - id: ""
            installation_id: ""
            private_key: ""
            repo_url: ""
          ssh_key:
    ```
=== "Example"
    ```yaml
    gitops:
      authentication:
        external_secrets:
          secretstore: ""
          github_app:
          - id: ""
            installation_id: ""
            private_key: ""
            repo_url: ""
          ssh_key:
    ```