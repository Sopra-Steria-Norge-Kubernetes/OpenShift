# Quick Start Guide - OpenShift Teams

This guide provides the minimum steps to create a new developer team, create your first tenant, and deploy your first application.

!!! note "Example Names"
    Throughout this guide, we use "team-poseidon" as the team name and "poseidon-web-app" and "poseidon-backend-app" as the application/tenant names. These are examples - replace them with your actual team and application names.

## Prerequisites

Before starting, ensure you have the following components:

### Required Components

1. **Infrastructure definitions** configured by Sopra Steria technician (contact your Sopra Steria representative)
2. **Team namespace repository** for Grafana configuration, secrets, and team-specific resources
3. **Application deployment repository** containing your application manifests and deployment configurations
4. **Azure Key Vault** for secret storage
5. **Access tokens** for accessing Key Vault and repositories

### Key Vault Requirements

Your Key Vault must contain these secrets:

- **Group Sync Secret** for RBAC integration
- **Repository Secret** for accessing private repositories  
- **Pull Secret** for Container Registry access (if not using public registries)

## Step 1: Understanding the Repository Structure

As part of the onboarding process, a repository named `infra-ocp4-tenants` will be established in your GitHub/Azure DevOps organization with this structure:

```plaintext
infra-ocp4-tenants/
├── team-definitions/
│   └── wave-1/
│       ├── team-poseidon.yaml
│       ├── team-hera.yaml
│       ├── team-zeus.yaml
│       └── team-apollo.yaml
└── application-definitions/
    ├── wave-1/
    │   ├── poseidon-web-app-tenant.yaml
    │   └── hera-web-app-tenant.yaml
    └── wave-2/
        ├── zeus-web-app-tenant.yaml
        └── apollo-web-app-tenant.yaml
```

!!! info "Wave Deployment Strategy"
    The "wave" directories determine the rollout sequence for Helm chart updates. Teams and tenants in `wave-1` receive changes first, followed by `wave-2`, and so on. This staged approach enables controlled, incremental deployments and easier troubleshooting.

## Step 2: Configure Your Team

### Team Configuration Location

Create your team configuration file in `team-definitions/wave-1/team-poseidon.yaml`.

### Basic Team Configuration

```yaml
teamname: team-poseidon  # Replace with your actual team name
values: |
  team:
    name: team-poseidon  # This will also be the team namespace name

  rbac:
    secret_name: team-poseidon-group-sync-secret  # Secret configured in Step 3
    team_edit: "AzureAD-Team-Poseidon-Developers"  # Replace with your write group
    team_view: "AzureAD-Team-Poseidon-Viewers"     # Replace with your read group
  
  secret_management:
    external_secrets:
      cluster_secret_stores: 
      - name: team-poseidon-secrets                                 # This name can be anything, name is just a reference
        tenant_id: 12345678-1234-1234-1234-123456789012             # Replace with your tenant ID
        keyvault_url: https://team-poseidon-kv.vault.azure.net/     # Replace with your Key Vault URL
        client_id: abcd1234-5678-90ab-cdef-123456789012             # Replace with your client ID
        client_secret: 8f2b7c1e-4d2a-4e5f-bc3e-9a1d2f3e4b5c         # Example Azure-style client secret
      - name: team-poseidon-app-secrets                             # This name can be anything, name is just a reference
        tenant_id: 12345678-1234-1234-1234-123456789012             # Replace with your tenant ID
        keyvault_url: https://team-poseidon-app-kv.vault.azure.net/ # Replace with your Key Vault URL
        client_id: abcd1234-5678-90ab-cdef-123456789012             # Replace with your client ID
        client_secret: ~ZxYvTgHjKlMnPqRsT1234567890abcdef           # Replace with your client secret name in Key Vault


```

### GitOps Authentication Options

Choose the authentication method that matches your Git provider:

=== "PAT (Personal Access Token)"

    ```yaml
    gitops:
      argocd:
        enable_auto_defined_apps: true
        team_repo_url: https://dev.azure.com/yourorg/poseidon-team/_git/openshift-config
        path: "applications"
      authentication:
        external_secrets:
          secretstore: team-poseidon-secrets
          pat:
          - repo_url: https://dev.azure.com/yourorg/poseidon-team/_git/team-config
            username: poseidon-team
            password: poseidon-team-pat-token
    ```

=== "GitHub App"

    ```yaml
    gitops:
      argocd:
        enable_auto_defined_apps: true
        team_repo_url: https://github.com/yourorg/poseidon-team-config
        path: "applications"
      authentication:
        external_secrets:
          secretstore: team-poseidon-secrets
          github_app:
          - id: 374237872
            installation_id: 8947359869
            private_key: poseidon-github-app-key
            repo_url: https://github.com/yourorg/poseidon-app-deployments
    ```
    
=== "SSH Key"

    ```yaml
    gitops:
      argocd:
        enable_auto_defined_apps: true
        team_repo_url: git@github.com:yourorg/poseidon-team-config.git
        path: "team-config"
      authentication:
        external_secrets:
          secretstore: team-poseidon-secrets
          ssh_key:
          - private_key: poseidon-ssh-private-key
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
teamname: team-poseidon
values: |
  team:
    name: team-poseidon

  rbac:
    secret_name: team-poseidon-group-sync-secret
    team_edit: "AzureAD-Team-Poseidon-Developers"
    team_view: "AzureAD-Team-Poseidon-Viewers"
  
  secret_management:
    external_secrets:
      cluster_secret_stores: 
      - name: team-poseidon-secrets
        tenant_id: 12345678-1234-1234-1234-123456789012
        keyvault_url: https://team-poseidon-kv.vault.azure.net/
        client_id: abcd1234-5678-90ab-cdef-123456789012
        client_secret: poseidon-kv-client-secret

  gitops:
    argocd:
      enable_auto_defined_apps: true
      team_repo_url: https://dev.azure.com/yourorg/poseidon-team/_git/openshift-config
      path: "applications"
    authentication:
      external_secrets:
        secretstore: team-poseidon-secrets
        pat:
        - repo_url: https://dev.azure.com/yourorg/poseidon-team/_git/team-config
          username: poseidon-team
          password: poseidon-team-pat-token
```

## Step 3: Configure Group Sync Secret

Create the group sync secret in your team repository. This secret must be stored in the Azure Key Vault configured in Step 2.

!!! warning "Important"
    The service principal used for group sync must have access to read the Azure AD groups configured in your team definition.

```yaml
apiVersion: external-secrets.io/v1beta1
kind: ExternalSecret
metadata:
  name: team-poseidon-group-sync-secret
  namespace: team-poseidon # Replace with your team namespace
spec:
  refreshInterval: 10m
  secretStoreRef:
    kind: ClusterSecretStore
    name: team-poseidon-team-poseidon-secrets-dev # Format: {namespace-name}-{secretstore-name}-{environment}
  target:
    name: team-poseidon-group-sync-secret # Must match secret_name in Step 2
    creationPolicy: Owner
    template:
      type: Opaque
  data:
    - secretKey: AZURE_CLIENT_ID
      remoteRef:
        key: poseidon-groupsync-client-id
    - secretKey: AZURE_CLIENT_SECRET
      remoteRef:
        key: poseidon-groupsync-client-secret
    - secretKey: AZURE_TENANT_ID
      remoteRef:
        key: poseidon-groupsync-tenant-id
```

## Step 4: Set Up Your Application Tenant

### Tenant Configuration Location

Create your tenant configuration file in `application-definitions/wave-1/poseidon-web-app.yaml`.

### Application Tenant Examples

=== "Web Application Tenant"

    ```yaml
    appname: poseidon-web-app
    values: |
      team:
        team_name: "team-poseidon"  # Must match your team name from Step 2

      namespace:
        name: poseidon-web-app
        description: "This tenant handles the Poseidon team's web application"
        displayName: "Poseidon Web Application"
        
        requests:
          enable: true
          memory: 2Gi
          cpu: 1
      
      environments:
        - name: dev # Creates namespace: poseidon-web-app-dev
          externalURLs:
            - https://api.external-service.com
            - https://database.yourorg.com
        - name: test # Creates namespace: poseidon-web-app-test
          externalURLs:
            - https://api.external-service.com
            - https://database.yourorg.com

      argocd:
        main_git_repository:
          repourl: https://github.com/yourorg/poseidon-app-deployments
          basepath: applicationsets
    ```

=== "Backend Application Tenant"

    ```yaml
    appname: poseidon-backend-app
    values: |
      team:
        team_name: "team-poseidon"

      namespace:
        name: poseidon-backend-app
        description: "This tenant handles the Poseidon team's backend API services"
        displayName: "Poseidon Backend API"
        
        requests:
          enable: true
          memory: 1Gi
          cpu: 0.5
      
      environments:
        - name: dev # Creates: poseidon-backend-app-dev
          externalURLs:
            - https://database.yourorg.com
            - https://auth-service.yourorg.com
        - name: test # Creates: poseidon-backend-app-test
          externalURLs:
            - https://database.yourorg.com
            - https://auth-service.yourorg.com

      argocd:
        main_git_repository:
          repourl: https://github.com/yourorg/poseidon-backend-deployments
          basepath: applicationsets
    ```

!!! info "Multiple Applications"
    Teams can manage multiple applications by creating separate tenant configuration files. Each application gets its own namespaces and can have different resource requirements.

### Tenant Configuration Reference

| Field | Description |
|-------|-------------|
| `namespace.name` | Base name for the tenant namespaces |
| `environments` | List of deployment environments (dev, test, prod) |
| `externalURLs` | External services your application needs to access |
| `argocd.main_git_repository` | GitOps configuration for automated deployments |

## Step 5: Structure Your Deployment Repository

Your deployment repository should follow this structure:

```
poseidon-app-deployments/
├── applicationsets/
│   ├── base/
│   │   ├── poseidon-web-app/
│   │   │   ├── kustomization.yaml
│   │   │   ├── deployment.yaml
│   │   │   ├── service.yaml
│   │   │   ├── servicemonitor.yaml
│   │   │   └── route.yaml
│   │   └── poseidon-backend-app/
│   │       ├── kustomization.yaml
│   │       ├── deployment.yaml
│   │       ├── service.yaml
│   │       ├── servicemonitor.yaml
│   │       └── route.yaml
│   ├── dev/
│   │   ├── poseidon-web-app/
│   │   │   └── kustomization.yaml
│   │   └── poseidon-backend-app/
│   │       └── kustomization.yaml
│   └── test/
│       ├── poseidon-web-app/
│       │   └── kustomization.yaml
│       └── poseidon-backend-app/
│           └── kustomization.yaml
```

### Key Components

- **`base/[app-name]/`**: Contains base configuration for each application
- **`{env}/[app-name]/`**: Environment-specific patches and customizations
- **`route.yaml`**: OpenShift Route for external access
- **`servicemonitor.yaml`**: Prometheus monitoring configuration

### OpenShift Route Example

```yaml
apiVersion: route.openshift.io/v1
kind: Route
metadata:
  name: poseidon-web-app
spec:
  to:
    kind: Service
    name: poseidon-web-app
  port:
    targetPort: http
  tls:
    termination: edge
```

## Step 6: Using Secrets in Applications

Reference secrets from your cluster secret store in your applications:

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

## Next Steps

1. **Commit your configurations** to the `infra-ocp4-tenants` repository
2. **Verify deployment** - Check that namespaces and applications are created
3. **Set up monitoring** - Configure alerts and dashboards
4. **Review security** - Ensure proper RBAC and network policies

## Troubleshooting

If you encounter issues:

1. **Verify secrets** are properly configured in Key Vault
2. **Check permissions** for access tokens and service principals
3. **Validate URLs** are accessible from the cluster
4. **Review ArgoCD logs** for deployment errors

For additional support, contact the platform team or refer to the detailed documentation.
