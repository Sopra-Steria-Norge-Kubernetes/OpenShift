# Quick Start Guide - OpenShift Teams

This guide will walk you through setting up a new developer team in our OpenShift environment, from initial prerequisites to deploying your first application.

!!! note "Example Names"
    Throughout this guide, we use "team-poseidon" as the team name and "poseidon-web-app" as the application/tenant name. These are examples - you should replace them with your actual team and application names.

## Prerequisites

Before you can enable a team and tenant for a running application, you must have the following components in place:

### Required Components

1. Infra definitions configured by Sopra Steria technician. contact your Sopra Steria contact to get the infra definitions configured.
2. **Team Namespace Repository** - For Grafana configuration, secrets, and other team-specific resources
3. **Application Deployment Repository** - Contains your application manifests and deployment configurations
4. **Azure Key Vault** - Azure Key Vault or equivalent secret storage solution
5. **Personal Access Token (PAT)** - For accessing Key Vault and repositories

### Key Vault Requirements

Your Key Vault must contain the following secrets:

- **Group Sync Secret** - For RBAC integration
- **Repository Secret** - For accessing private repositories
- **Pull Secret** - For Container Registry access (if not using public registries)

## Step 1: Location of team/tenant definitions files
In your github/ado organization there should be a repository named 'infra-ocp4-tenants' 
it should contain a directory structure like this:

```plaintext
infra-ocp4-tenants/
├── team-definitions/
│   └── wave-1/
│       ├── team-poseidon.yaml
│       ├── team-hera.yaml
│       ├── team-zeus.yaml
│       └── team-apollo.yaml
└── applications-definitions/
    ├── wave-1/
    │   ├── poseidon-web-app-tenant.yaml
    │   └── hera-web-app-tenant.yaml
    └── wave-2/
        ├── zeus-web-app-tenant.yaml
        └── apollo-web-app-tenant.yaml
```
The "wave" directories for teams and tenants determine the rollout sequence for updates to the respective Helm charts. Teams and tenants in `wave-1` receive changes first, followed by those in `wave-2`, and so on. This staged approach allows for controlled, incremental deployments and easier troubleshooting during upgrades.
## Step 1: Configure Your Team

### Location of Team Configuration
Should be placed in the `values.yaml` file of team-definitions/wave-x/ directory. the filename should be named after the team you want to create, for instance `team-poseidon.yaml`.

### Basic team configuration with azure keyvault
```yaml
teamname: team-poseidon  # Replace with your actual team name
values: |
  team:
    name: team-poseidon  # Replace with your actual team name

  rbac:
    secret_name: team-poseidon-group-sync-secret  # Replace with your groupsync secret name
    team_edit: "AzureAD-Team-Poseidon-Developers"  # Replace with your write group
    team_view: "AzureAD-Team-Poseidon-Viewers"     # Replace with your read group
  
  secret_management:
    external_secrets:
      cluster_secret_stores: 
      - name: team-poseidon-secrets  # This name can be anything, but must be referenced in gitops authentication or group sync
        tenant_id: 12345678-1234-1234-1234-123456789012  # Replace with your tenant ID
        keyvault_url: https://team-poseidon-kv.vault.azure.net/  # Replace with your Key Vault URL
        client_id: abcd1234-5678-90ab-cdef-123456789012   # Replace with your client ID
        client_secret: poseidon-kv-client-secret           # Replace with your client secret name in Key Vault
      - name: team-poseidon-app-secrets  # This name can be anything, but must be referenced in application deployments
        tenant_id: 12345678-1234-1234-1234-123456789012  # Replace with your tenant ID
        keyvault_url: https://team-poseidon-app-kv.vault.azure.net/  # Replace with your Key Vault URL
        client_id: abcd1234-5678-90ab-cdef-123456789012   # Replace with your client ID
        client_secret: poseidon-app-kv-client-secret           # Replace with your client secret name in Key Vault


```

### Gitops authentication options
=== "PAT (Personal Access Token)"

    ```yaml
    gitops:
      argocd:
        enable_auto_defined_apps: true # This should default to false in the chart
        team_repo_url: https://dev.azure.com/yourorg/poseidon-team/_git/openshift-config  # Replace with your team repo URL
        path: "applications"  # Replace with your config folder path
      authentication:
        external_secrets:
          secretstore: team-poseidon-secrets # References cluster_secret_stores configured above
          pat: # If your repo requires PAT for access (Azure DevOps Git is one example)
          - repo_url: https://dev.azure.com/yourorg/poseidon-team/_git/team-config  # Replace with your deployment repo URL
            username: poseidon-team # Replace with your PAT username
            password: poseidon-team-pat-token # Replace with your PAT secret name in Key Vault
    ```

=== "Github App"

    ```yaml
    gitops:
      argocd:
        enable_auto_defined_apps: true # This should default to false in the chart
        team_repo_url: https://github.com/yourorg/poseidon-team-config  # Replace with your team repo URL
        path: "applications"  # Replace with your config folder path
      authentication:
        external_secrets:
          secretstore: team-poseidon-secrets # References cluster_secret_stores configured above
          github_app:
          - id: 374237872  # Replace with your GitHub App ID
            installation_id: 8947359869  # Replace with your installation ID
            private_key: poseidon-github-app-key  # Replace with your private key secret name in Key Vault
            repo_url: https://github.com/yourorg/poseidon-app-deployments  # Replace with your deployment repo URL
    ```
    
=== "SSH Key"

    ```yaml
    gitops:
      argocd:
        enable_auto_defined_apps: true # This should default to false in the chart
        team_repo_url: git@github.com:yourorg/poseidon-team-config.git  # Replace with your team repo URL
        path: "team-config"  # Replace with your config folder path
      authentication:
        external_secrets:
          secretstore: team-poseidon-secrets # References cluster_secret_stores configured above
          ssh_key:
          - private_key: poseidon-ssh-private-key  # Replace with your SSH private key secret name in Key Vault
            type: git
            repo_url: git@github.com:yourorg/poseidon-app-deployments.git  # Replace with your deployment repo URL
    ```

=== "Helm Repository"

    ```yaml
    gitops:
      argocd:
        enable_auto_defined_apps: true # This should default to false in the chart
        team_repo_url: https://github.com/yourorg/poseidon-team-config  # Replace with your team repo URL
        path: "applications"  # Replace with your config folder path
      authentication:
        external_secrets:
          secretstore: team-poseidon-secrets # References cluster_secret_stores configured above
          helm_registry:
          - username: poseidon-acr-user     # Replace with your ACR username secret name in Key Vault
            password: poseidon-acr-token    # Replace with your ACR token secret name in Key Vault
            registry_url: poseidonteam.azurecr.io  # Replace with your ACR URL
    ```
### Example full team configuration
```yaml
teamname: team-poseidon  # Replace with your actual team name
values: |
  team:
    name: team-poseidon  # Replace with your actual team name

  rbac:
    secret_name: poseidon-groupsync-secret  # Replace with your groupsync secret name
    team_edit: "AzureAD-Team-Poseidon-Developers"  # Replace with your write group
    team_view: "AzureAD-Team-Poseidon-Viewers"     # Replace with your read group
  
  secret_management:
    external_secrets:
      cluster_secret_stores: 
      - name: team-poseidon-secrets  # This name can be anything, but must be referenced in application deployments
        tenant_id: 12345678-1234-1234-1234-123456789012  # Replace with your tenant ID
        keyvault_url: https://team-poseidon-kv.vault.azure.net/  # Replace with your Key Vault URL
        client_id: abcd1234-5678-90ab-cdef-123456789012   # Replace with your client ID
        client_secret: poseidon-kv-client-secret           # Replace with your client secret name in Key Vault

  gitops:
    argocd:
    enable_auto_defined_apps: true # This should default to false in the chart
    team_repo_url: https://dev.azure.com/yourorg/poseidon-team/_git/openshift-config  # Replace with your team repo URL
    path: "applications"  # Replace with your config folder path
    authentication:
      external_secrets:
        secretstore: team-poseidon-secrets # References cluster_secret_stores configured above
        pat: # If your repo requires PAT for access (Azure DevOps Git is one example)
        - repo_url: https://dev.azure.com/yourorg/poseidon-team/_git/team-config  # Replace with your deployment repo URL
          username: poseidon-team # Replace with your PAT username
          password: poseidon-team-pat-token # Replace with your PAT secret name in Key Vault
```

## Step 2: Configure group sync
Must be added to team repository, this secret should be stored in the azure keyvault configured in the previous step. and should have access to the groups configured in the team-definitions file under rbac:.

=== "Group Sync Secret"

    ```yaml
    apiVersion: external-secrets.io/v1beta1
    kind: ExternalSecret
    metadata:
      name: team-poseidon-group-sync-secret
      namespace: team-poseidon
    spec:
      refreshInterval: 10m
      secretStoreRef:
        kind: ClusterSecretStore
        name: team-poseidon-secrets
      target:
        name: team-poseidon-group-sync-secret # used in step 1
        creationPolicy: Owner
        template:
          type: Opaque
      data:
        - secretKey: AZURE_CLIENT_ID
          remoteRef:
            key: poseidon-groupsync-client-id     # Replace with your Key Vault secret name
        - secretKey: AZURE_CLIENT_SECRET
          remoteRef:
            key: poseidon-groupsync-client-secret # Replace with your Key Vault secret name
        - secretKey: AZURE_TENANT_ID
          remoteRef:
            key: poseidon-groupsync-tenant-id     # Replace with your Key Vault secret name
    ```

## Step 3: Set Up Your Tenant

### Location of Tenant Configuration
Should be placed in the `values.yaml` file of applications-definitions/wave-x/ directory file is often named after the tenant you want to create. for instance `poseidon-web-app-tenant.yaml`.

=== "Basic Configuration"

    ```yaml
    appname: poseidon-web-app  # Replace with your application name
    values: |
      team:
        team_name: "team-poseidon"  # Must match your team name from Step 1

      namespace:
        name: poseidon-web-app  # Replace with your tenant/namespace name
        description: "This tenant handles the Poseidon team's web application"  # Replace with your description
        displayName: "Poseidon Web Application"  # Replace with your display name
        
        requests:
          enable: true
          memory: 2Gi    # Adjust based on your application needs
          cpu: 1         # Adjust based on your application needs
      
      environments:
        - name: dev # Will be appended to "namespace.name" -> poseidon-web-app-dev
          externalURLs:
            - https://api.external-service.com  # Replace with URLs your app needs to reach
            - https://database.yourorg.com      # Replace with URLs your app needs to reach
        - name: test # Will be appended to "namespace.name" -> poseidon-web-app-test
          externalURLs:
            - https://api.external-service.com  # Replace with URLs your app needs to reach
            - https://database.yourorg.com      # Replace with URLs your app needs to reach

      argocd:
        main_git_repository:
          repourl: https://github.com/yourorg/poseidon-app-deployments  # Replace with your deployment repository URL
          basepath: applicationsets  # Replace with your base path if different
    ```

=== "Second Application Example"

    ```yaml
    appname: poseidon-backend-app  # Replace with your backend application name
    values: |
      team:
        team_name: "team-poseidon"  # Must match your team name from Step 1

      namespace:
        name: poseidon-backend-app  # Replace with your tenant/namespace name
        description: "This tenant handles the Poseidon team's backend API services"  # Replace with your description
        displayName: "Poseidon Backend API"  # Replace with your display name
        
        requests:
          enable: true
          memory: 1Gi    # Adjust based on your application needs (backend might need less memory)
          cpu: 0.5       # Adjust based on your application needs
      
      environments:
        - name: dev # Will be appended to "namespace.name" -> poseidon-backend-app-dev
          externalURLs:
            - https://database.yourorg.com      # Replace with URLs your backend needs to reach
            - https://auth-service.yourorg.com  # Replace with URLs your backend needs to reach
        - name: test # Will be appended to "namespace.name" -> poseidon-backend-app-test
          externalURLs:
            - https://database.yourorg.com      # Replace with URLs your backend needs to reach
            - https://auth-service.yourorg.com  # Replace with URLs your backend needs to reach

      argocd:
        main_git_repository:
          repourl: https://github.com/yourorg/poseidon-backend-deployments  # Replace with your backend deployment repository URL
          basepath: applicationsets  # Replace with your base path if different
    ```

!!! info "Multiple Applications per Team"
    Teams can manage multiple applications by creating separate tenant configuration files for each application. Each application gets its own set of namespaces and can have different resource requirements and external URL configurations.

### Tenant Configuration Explanation

- **namespace**: Defines the Kubernetes namespace and resource limits
- **environments**: List of deployment environments (dev, test, prod, etc.), prod is often created in another cluster with a separate tenant configuration
- **externalURLs**: External services your application needs to access
- **argocd**: GitOps configuration for automated deployments

## Step 4: Structure Your Deployment Repository

=== "Standard Kustomize Structure"

    ```
    poseidon-app-deployments/              # Replace with your repository name
    ├── applicationsets/
    │   ├── base/                          # Base configurations
    │   │   ├── poseidon-web-app/          # Web application base
    │   │   │   ├── kustomization.yaml     # Base kustomization configuration
    │   │   │   ├── deployment.yaml        # Web app deployment manifest
    │   │   │   ├── service.yaml           # Web app service manifest
    │   │   │   └── servicemonitor.yaml    # ServiceMonitor for Prometheus
    │   │   └── poseidon-backend-app/      # Backend application base
    │   │       ├── kustomization.yaml     # Base kustomization configuration
    │   │       ├── deployment.yaml        # Backend app deployment manifest
    │   │       ├── service.yaml           # Backend app service manifest
    │   │       └── servicemonitor.yaml    # ServiceMonitor for Prometheus
    │   ├── dev/                           # Development environment
    │   │   ├── poseidon-web-app/          # Web app dev configuration
    │   │   │   └── kustomization.yaml     # Dev environment-specific patches
    │   │   └── poseidon-backend-app/      # Backend app dev configuration
    │   │       └── kustomization.yaml     # Dev environment-specific patches
    │   └── test/                          # Test environment
    │       ├── poseidon-web-app/          # Web app test configuration
    │       │   └── kustomization.yaml     # Test environment-specific patches
    │       └── poseidon-backend-app/      # Backend app test configuration
    │           └── kustomization.yaml     # Test environment-specific patches
    ```
    
### Repository Structure Explanation

- **applicationsets/base/[app-name]/**: Contains base configuration specific to each application
- **applicationsets/dev/[app-name]/, test/[app-name]/, etc.**: Environment-specific configurations and patches for each application
- **kustomization.yaml**: Kustomize configuration files for managing variants per application and environment

This structure allows each application to have its own base configuration while still maintaining environment-specific customizations.

## Step 4: Secret Management Configuration

### Using Secret Stores in Applications

To add a secret to application deployment you can use the `ExternalSecret` resource to reference secrets from your cluster secret store configured in Step 1.

```yaml
# For application secrets (database, APIs, etc.)
apiVersion: external-secrets.io/v1beta1
kind: ExternalSecret
metadata:
  name: poseidon-database-secret
spec:
  secretStoreRef:
    name: team-poseidon-app-secrets  # Reference the team secret store
    kind: ClusterSecretStore
  target:
    name: database-credentials
  data:
  - secretKey: connection-string
    remoteRef:
      key: poseidon-db-connection-string
```