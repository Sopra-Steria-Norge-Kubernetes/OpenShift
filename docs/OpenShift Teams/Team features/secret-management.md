# Secret Management Feature

The secret managment feature is used to create global ClusterSecretStores in the team namespace, which can be utilized by multiple tenants that are a member of the team where the ClusterSecretStore is defined.

You can read more in depth about this feature by following this link: [Secret Managment](../../OpenShift%20Tenants/Tenant%20features/external-secrets.md)

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