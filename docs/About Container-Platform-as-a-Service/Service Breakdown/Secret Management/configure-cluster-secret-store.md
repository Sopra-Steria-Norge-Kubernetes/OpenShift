# Configure ClusterSecretStore for Managing Team Secrets 

This guide provides step-by-step instructions for configuring a ClusterSecretStore to enable secure, team-level secret management in OpenShift. Once configured, your team can use ExternalSecret to deploy sensitive data to your team’s tenants and namespaces in a GitOps-friendly way.


!!! note
    This guide uses **Azure Key Vault** as the external secret management provider.

---

### Prerequisites

Ensure you have the following:

- An Azure Key Vault instance dedicated to your team  
- An Azure App Registration with associated ClientID and ClientSecret
- The App Registration must be assigned the Key Vault Secrets User role for the relevant Key Vault
- Your Azure Tenant ID
- The `kubeseal` and `yq` CLI tools installed locally  

---

### Encrypting ClusterSecretStore Credentials

To authenticate with Azure Key Vault, OpenShift requires credentials from an Azure App Registration. Since these credentials are stored in a Git repository, they must be encrypted to ensure confidentiality. This is achieved using SealedSecrets, which provides one-way encryption such that only the SealedSecrets controller running within the OpenShift cluster can decrypt the data. 

You can encrypt the credentials using the `scripts/encrypt_client_credentials.sh` script in your tenant repository, or follow [this guide](some/guide) to do it manually.

Run the script as follows:

=== "Script"
    ```text
    $ ./scripts/encrypt_client_credentials.sh

    Enter Client ID: <Client_ID>
    Enter Client Secret: <Client_Secret>
    Enter Team Name (namespace): <Team_Name>
    Which cluster will the secret be deployed to?
    1. test
    2. prod
    Enter 1 or 2: <1_or_2>
    ```

=== "Example"
    ```text
    $ ./scripts/encrypt_client_credentials.sh

    Enter Client ID: 3b9f8a4e-1d2c-4567-9e8f-abc123def456
    Enter Client Secret: vN7~eFgL9qT#8wzX2!aMkH4JtPsD0rXeKpC7v
    Enter Team Name (namespace): team-poseidon
    Which cluster will the secret be deployed to?
    1. test
    2. prod
    Enter 1 or 2: 1
    ```

The script will seal your credentials and print the encrypted values to standard output. These values will be used in the next step to configure the `ClusterSecretStore` as part of your team’s secret management setup.

---

### Configuring ClusterSecretStore in Your Team Setup

After sealing your credentials, add them to your team’s configuration file under `secret_management`, together with the required values. Refer to the example below for guidance.

=== "Secret Management Configuration"
    ```yaml
    secret_management:
      external_secrets:
        cluster_secret_stores: 
        - name: <cluster_secret_stores_name>
          tenant_id: <azure_tenant_id> 
          keyvault_url: <keyvault_url> 
          client_id: <encrypted_client_id> 
          client_secret: <encrypted_client_secret> 
    ```

=== "Example"
    ```yaml
    secret_management:
      external_secrets:
        cluster_secret_stores: 
        - name: gitops
          tenant_id: 8f3c5b3a-12d4-4e9f-9b92-7f04d2c44abc
          keyvault_url: https://keyvault-team-poseidon-test.vault.azure.net/
          client_id: AgAlMkJ1FkdAaFFebrbwMsadZTdlz3BgP2dtsI3FZJmIl3McPD[...]
          client_secret: AgB4MfXJu6oX4I3F+5JG1hSFHCnTtq9IdgdfhaL1Aw0HbX[...]
    ```

---

## Final Step: Submit Your Changes

To complete the setup, commit your changes and create a pull request in your team’s repository. Once the pull request is reviewed and merged, the ClusterSecretStore configuration will be active, and your team can begin using ExternalSecrets to manage secrets across tenants and namespaces.