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
      enabled: true
      url: ""
      realm: "" # Global variable - per customer/team
      # If the service account/user is in another realm.  Change this.
      # Default: `master`
      loginRealm: "master"
...
```

### LDAP (Active Directory)

An alternative to Azure is provided by using `ldap.enabled: true`. It uses the
same group parameters (`ad_group_*`) — for LDAP these are the group **CN**s, and
the chart resolves each to its full directory DN using `groups_base_dn`.

```yaml
...
  rbac:
    ad_group_write_access: <group CN with write access>
    ad_group_read_access: <group CN with read access>

    ldap:
      enabled: true
      url: ldaps://<your-ldap-server>
      base_dn: <directory search base, e.g. dc=example,dc=com>
      groups_base_dn: <OU that contains the access groups, e.g. OU=Access,OU=Groups,DC=example,DC=com>
...
```

Nested group membership is resolved recursively on the Active Directory side, so
members of nested sub-groups are included automatically.

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
| `ldap.enabled`                              | Use LDAP/Active Directory group sync                | "true"                         | Boolean  | `false`           |
| `ldap.url`                                  | LDAP server URL (use `ldaps://` for TLS)            | "ldaps://dc01.example.com"     | String   | ""                |
| `ldap.base_dn`                              | Search base shared by the user and group queries    | "dc=example,dc=com"            | String   | ""                |
| `ldap.groups_base_dn`                       | OU containing the access groups; also used to build each group's DN (`CN=<group>,<groups_base_dn>`) | "OU=Access,OU=Groups,DC=example,DC=com" | String | value of `base_dn` |
| `ldap.users_base_dn`                        | Optional override for the user search base          | "OU=Users,DC=example,DC=com"   | String   | value of `base_dn` |


## Further reading
- [Kubernetes Offical Documentation - Role Based Access Control](https://kubernetes.io/docs/reference/access-authn-authz/rbac/)
- [OpenShift Offical Documentation - Role Based Access Control](https://docs.openshift.com/container-platform/4.13/authentication/using-rbac.html)
