# Quick Start Guide - OpenShift Tenants

This guide provides the minimum steps to create a tenant and deploy your first application without requiring a pre-configured team.
Key differences from the team-based approach include:

- **No team-based RBAC**: Access groups are configured directly in the tenant YAML files
- **No team-based secret management**: Secrets are managed directly in the tenant configuration
- **No team-based GitOps**: Each tenant has its own GitOps configuration
- **No team-based monitoring**: Monitoring is configured directly in the tenant

!!! note "Example Names"
    Throughout this guide, we use "poseidon-web-app" and "poseidon-backend-app" as the application/tenant names. These are examples - replace them with your actual application names.

## Prerequisites

Before starting, ensure you have the following:

### Required Components

1. **Infrastructure definitions** configured by Sopra Steria technician (contact your Sopra Steria representative)
2. **Application deployment repository** containing your application manifests and deployment configurations
3. **Access to the `infra-ocp4-tenants` repository** in your GitHub/Azure DevOps organization
4. **Azure Key Vault** for secret storage (optional)
5. **Access tokens** for accessing repositories

## Step 1: Understanding the Repository Structure

Tenants are configured in the `application-definitions` section of the `infra-ocp4-tenants` repository:

```plaintext
infra-ocp4-tenants/
└── application-definitions/
    ├── wave-1/
    │   ├── poseidon-web-app-tenant.yaml    # Your new tenant
    │   └── poseidon-backend-app-tenant.yaml # Another tenant (optional)
    └── wave-2/
        └── another-app-tenant.yaml
```

!!! info "Wave Deployment Strategy"
    The "wave" directories determine the rollout sequence for Helm chart updates. Tenants in `wave-1` receive changes first, followed by `wave-2`, and so on. This staged approach enables controlled, incremental deployments and easier troubleshooting.

## Step 2: Configure Your Tenant

### Tenant Configuration Location

Create your tenant configuration file in `application-definitions/wave-1/poseidon-web-app-tenant.yaml`.

### Tenant Examples

=== "Basic Tenant"

    ```yaml
    appname: zeus
    values: |
      namespace:
        name: zeus
        description: "zeus"
        displayName: "zeus"        
        requests:
          enable: true
          memory: 1Gi
          cpu: 1
        
      monitoring:
        storageAlertsEnabled: false
      environments:
        - name: dev
          externalURLs:
            - testurl.com
            - login.microsoftonline.com
        - name: test
          externalURLs:
            - woop.com
            - hello.net

      rbac:
        secret_name: "zeus-groupsync-secret" # This might allready have been defined globally.
        ad_group_write_access: "EXAMPLE_OpenShift_Zeus_Write"
        ad_group_read_access: "EXAMPLE_OpenShift_Zeus_Read"

      argocd:
        main_git_repository:
          repourl: "https://git.example.com/example-org/zeus-deployments.git"
          basepath: applicationsets

      gitops:
        authentication:
          external_secrets:
            secretstore: zeus-secrets
            github_app:
            - repo_url: "https://git.example.com/example-org/zeus-deployments"
              id: "123456"
              installation_id: "987654321"
              private_key: "github-app-zeus-private-key"
            helm_registry:
            - registry_url: "registry.example.com"
              username: "svc-zeus-gitops"
              password: "kv-zeus-acr-token"

      secret_management:
        external_secrets:
          cluster_secret_stores:
          - name: zeus-secrets
            environment: dev
            tenant_id: "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa"
            keyvault_url: "https://kv-zeus.invalid.vault.azure.net/"
            client_id: "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb"
            client_secret: ~ZxYvTgHjKlMnPqRsT1234567890abcdef           # Replace with your client secret name in Key Vault
    ```

!!! info "Multiple Applications"
    You can create multiple tenants by creating separate tenant configuration files. Each application gets its own namespaces and can have different resource requirements.

## Step 3: GitOps Configuration

Choose an authentication method for Argo CD to access your repositories.

=== "PAT (Personal Access Token)"

    ```yaml
    gitops:
      argocd:
        enable_auto_defined_apps: true
        team_repo_url: https://dev.azure.com/yourorg/tenant/_git/openshift-config
        path: "applications"
      authentication:
        external_secrets:
          secretstore: zeus-secrets
          pat:
          - repo_url: https://dev.azure.com/yourorg/tenant/_git/deployments
            username: svc-zeus
            password: zeus-pat-token
    ```

=== "GitHub App"

    ```yaml
    gitops:
      argocd:
        enable_auto_defined_apps: true
        team_repo_url: https://github.com/yourorg/tenant-config
        path: "applications"
      authentication:
        external_secrets:
          secretstore: zeus-secrets
          github_app:
          - id: 374237872
            installation_id: 8947359869
            private_key: zeus-github-app-key
            repo_url: https://github.com/yourorg/tenant-deployments
    ```

=== "SSH Key"

    ```yaml
    gitops:
      argocd:
        enable_auto_defined_apps: true
        team_repo_url: git@github.com:yourorg/tenant-config.git
        path: "applications"
      authentication:
        external_secrets:
          secretstore: zeus-secrets
          ssh_key:
          - private_key: zeus-ssh-private-key
            repo_url: git@github.com:yourorg/tenant-deployments.git
    ```

=== "Helm Repository"

    ```yaml
    gitops:
      argocd:
        enable_auto_defined_apps: true
        team_repo_url: https://github.com/yourorg/tenant-config
        path: "applications"
      authentication:
        external_secrets:
          secretstore: zeus-secrets
          helm_registry:
          - username: zeus-acr-user
            password: zeus-acr-token
            registry_url: zeusteam.azurecr.io
    ```

## Step 4: RBAC Configuration

Configure access groups directly in your tenant and provide credentials via secrets (or External Secrets).

```yaml
# Example ExternalSecret for Group Sync credentials (optional)
apiVersion: external-secrets.io/v1beta1
kind: ExternalSecret
metadata:
  name: zeus-group-sync-secret
  namespace: zeus
spec:
  refreshInterval: 10m
  secretStoreRef:
    kind: ClusterSecretStore
    name: zeus-secrets
  target:
    name: zeus-group-sync-secret
    creationPolicy: Owner
    template:
      type: Opaque
  data:
    - secretKey: AZURE_CLIENT_ID
      remoteRef:
        key: zeus-groupsync-client-id
    - secretKey: AZURE_CLIENT_SECRET
      remoteRef:
        key: zeus-groupsync-client-secret
    - secretKey: AZURE_TENANT_ID
      remoteRef:
        key: zeus-groupsync-tenant-id
```

## Step 5: Structure Your Deployment Repository

Your deployment repository should follow this structure:

=== "Standard Kustomize Structure"

    ```
    poseidon-app-deployments/              # Replace with your repository name
    ├── applicationsets/
    │   ├── base/                          # Base configurations
    │   │   ├── poseidon-web-app/          # Web application base
    │   │   │   ├── kustomization.yaml     # Base kustomization configuration
    │   │   │   ├── deployment.yaml        # Web app deployment manifest
    │   │   │   ├── service.yaml           # Web app service manifest
    │   │   │   ├── route.yaml             # OpenShift route for external access
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

=== "Multiple Repository Structure"

    ```
    # Web Application Repository
    poseidon-web-app-deployments/
    ├── applicationsets/
    │   ├── base/
    │   │   └── poseidon-web-app/
    │   │       ├── kustomization.yaml
    │   │       ├── deployment.yaml
    │   │       ├── service.yaml
    │   │       └── route.yaml
    │   ├── dev/
    │   │   └── poseidon-web-app/
    │   │       └── kustomization.yaml
    │   └── test/
    │       └── poseidon-web-app/
    │           └── kustomization.yaml

    # Backend Application Repository  
    poseidon-backend-deployments/
    ├── applicationsets/
    │   ├── base/
    │   │   └── poseidon-backend-app/
    │   │       ├── kustomization.yaml
    │   │       ├── deployment.yaml
    │   │       └── service.yaml
    │   ├── dev/
    │   │   └── poseidon-backend-app/
    │   │       └── kustomization.yaml
    │   └── test/
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

Reference secrets from your tenant configuration:

```yaml
# Examples of using secrets in tenant applications will be added here
# This will show how to reference and use secrets without team-based secret stores
```

## Step 7: Advanced Configuration

### Resource Quotas
Each tenant must have a clusterquota defined with a request. this is how much compute you expect your applications to use. If you define like the example below, your applications cant combined exceed this amount.

```yaml
  requests:
    memory: 1Gi
    cpu: 1
```
### External Traffic
Based on the specific loadbalancer configuration outside of Openshift the steps to get traffic from internett into your application might differ. You will allways be able to contact the `Openshift Route` from the same vlan as the openshift environment, so for testing this might suffice

## Step 9: Deployment Process

1. **Commit tenant configuration** to the `infra-ocp4-tenants` repository
2. **Prepare deployment repository** with the proper structure
3. **Commit application manifests** to your deployment repository
4. **Verify deployment** - Check that namespaces and applications are created
5. **Test connectivity** - Ensure external URLs are accessible

For additional support, contact the platform team or refer to the detailed documentation sections.