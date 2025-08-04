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

## In-depth description of parameters

The `rbac` feature includes the following variables:

| <div style="width:160px">**Variable**</div>                   | **Description**                                                  | **Example**                    | **Type**  | **Defualt Value**
|-------------------------------|------------------------------------------------------------------|--------------------------------|-----------|-------|
| `ad_group_write_access`       | Azure AD group given admin access to the tenant                  | AD-poseidon1-write        | String    | "" |
| `ad_group_read_access`        | Azure AD group given read-only access to the tenant              | AD-poseidon1-read        | String    | "" |


## Further reading
- [Kubernetes Offical Documentation - Role Based Access Control](https://kubernetes.io/docs/reference/access-authn-authz/rbac/)
- [OpenShift Offical Documentation - Role Based Access Control](https://docs.openshift.com/container-platform/4.13/authentication/using-rbac.html)