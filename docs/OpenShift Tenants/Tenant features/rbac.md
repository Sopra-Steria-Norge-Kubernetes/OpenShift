# Role Based Access Control

## What is Role-Based Access Control (RBAC)?

Role-Based Access Control (RBAC) in OpenShift is a method of regulating access to resources based on the roles of individual users within your organization.

## How to configure RBAC

To configure RBAC settings for your OpenShift tenant using a Helm chart, include the following YAML configuration in your Helm values file:

``` yaml
...
  rbac:
    ad_group_write_access: <Azure AD group with write access>
    ad_group_read_access: <Azure AD group with read access>

    keycloak:
      enabled: false
      url: ""
      realm: "" # Global variable - per customer/team
      # If the service account/user is in another realm.  Change this.
      # Default: `master`
      loginRealm: "master"
...
```

### Keycloak

An alternative to Azure is provided by using `keycloak.enabled: true`.

Uses the same parameter for groups (`ad_`).

```yaml
...
  rbac:
    ad_group_write_access: <Azure AD group with write access>
    ad_group_read_access: <Azure AD group with read access>

    keycloak:
      enabled: false
      url: ""
      realm: "" # Global variable - per customer/team
      # If the service account/user is in another realm.  Change this.
      # Default: `master`
      loginRealm: "master"
...
```

## In-depth description of parameters

The `rbac` feature includes the following variables:

| <div style="width:160px">**Variable**</div> | **Description**                                     | **Example**                    | **Type** | **Defualt Value** |
|---------------------------------------------|-----------------------------------------------------|--------------------------------|----------|-------------------|
| `ad_group_write_access`                     | Azure AD group given admin access to the tenant     | AD-poseidon1-write             | String   | ""                |
| `ad_group_read_access`                      | Azure AD group given read-only access to the tenant | AD-poseidon1-read              | String   | ""                |
| `keyclaok.enable`                           | Use Keycloak                                        | "true"                         | Boolean  | `false`           |
| `keycloak.url`                              | FQDN to Keycloak instance                           | "https://keycloak.example.com" | String   | ""                |
| `keycloak.realm`                            | Realm where groups reside                           | "customer"                     | String   | ""                |
| `keycloak.loginRealm`                       | Alternative realm used for login                    | "master"                       | String   | `master`          |


## Further reading
- [Kubernetes Offical Documentation - Role Based Access Control](https://kubernetes.io/docs/reference/access-authn-authz/rbac/)
- [OpenShift Offical Documentation - Role Based Access Control](https://docs.openshift.com/container-platform/4.13/authentication/using-rbac.html)
