# OpenShift Tenant configuration for Private Helm Registry

Configuring the OpenShift Tenant to use a private Helm Registry requires the following steps:
1. Generate a sealedSecret with the required values
2. Add the sealedSecrets to your OpenShift Tenant  

## Configuring a Sealed Secret for integration with Argo CD

Before moving on to the next step we need to have the appropriate values noted down. **Note that the chosen method of authentication will dictate the values needed for the username and password.**

The following values are not dependant on the authentication method:

- **url**: ACR Login server
- **name**: ACR Name

The following values are required if **method 1** is chosen for authentication:

- **username**: ACR username
- **password**: access token

The following values are required if **method 2** is chosen for authentication:

- **username:** Application ID (Client ID)
- **password:** Application Secret (Client Secret)

Encode each of your values with the following terminal command:

```
echo -n '<RAW_VALUE>' | base64
```

Create a generic secrets.yml and insert your encoded values. The following code snippet shows how this can be done correctly:

```yaml title="secrets.yml"
apiVersion: v1
kind: Secret
metadata:
  name: example
  namespace: gitops-developers
type: Opaque
Data:
  name: <ACR_NAME_BASE64_ENCODED>
  enableOCI: <BASE64_ENCODED> # base64 encoded value of 'True'
  type: <BASE64_ENCODED> # base64 encoded value of 'helm'
  password: <ACR_ACCESS_TOKEN__BASE64_ENCODED>
  username: <ACR_USERNAME_BASE64_ENCODED>
  url: <ACR_LOGIN_SERVER_BASE64_ENCODED>
```

Generate your sealed secret values using the following kubeseal command:

```
kubeseal --cert <PATH_TO_CERTIFICATE> --scope namespace-wide -f <PATH_TO_SECRETS_FILE> -n gitops-developers -o yaml
```

In the tenant definition file configure the gitops section and insert your sealed secret values. When empty, this is what section looks like:

```yaml title="values.yaml"
  gitops:
    helm_registry:
      enable_custom_helm_registries: false
      enableOCI: "" # Global variable - decrypted value true for namespace gitops-developer - Encrypted and sat by cluster admins
      type: "" # Global variable - decrypted helm for namespace gitops-developer - Encrypted and sat by cluster admins
      helm_registries:
      - repository_name: "" # Sealed secret ACR Name
        url: "" # Sealed secret ACR Login Server
        password: "" # Sealed secret ACR Access Token
        username: "" # Sealed secret ACR Username
```