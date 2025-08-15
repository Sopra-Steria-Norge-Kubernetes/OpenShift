---
Author: GitHub Copilot
Title: Secret Management Introduction
Version: 1.0
externally-exposed: true
---

# Secret Management Introduction

OpenShift Container Platform-as-a-Service integrates secret management with the **Teams** and **Tenants** model for centralized credentials and secure, isolated access.

Both External Secrets and Sealed Secrets provide Kubernetes/OpenShift secrets, covering all secret types and use cases described in [Creating and Managing OpenShift Secrets](creating-and-managing-openshift-secrets.md).

## What We Deliver
### External Secrets

Integrates with external Key Management System (KMS) (e.g., Azure Key Vault) to fetch and sync secrets:

- Dynamic retrieval and automatic rotation  
- GitOps-friendly, no sensitive data in Git  
- Centralized provider management  
  

More information about External Secret and how it is integrated into OpenShift Tenant and Teams can be found [here](../Secret Management/External Secrets/Introduction.md).


### Sealed Secrets
Encrypts secrets for safe Git storage without external dependencies:

- One-way encryption, cluster-only decryption  
- Git-safe storage  
- Simple GitOps workflow integration
- Suitable if you can't use KMS

More information about External Secret and how it is integrated into OpenShift Tenant and Teams can be found [here](../Secret Management/Sealed Secrets/Introduction.md).

### Kubernetes Secrets

Both External Secrets and SealedSecrets creates a Kubernetes Secret in the end, so it is important to understand how these objects work and how you can fit them into your application. 
OpenShift accommodates various secret types, each designed to meet specific application requirements. All secret values in OpenShift are base64 encoded, adding a layer of data protection. You can encode your secrets using the Linux CLI command: 
```BASH
echo -n 'mysecret' | base64 
```

#### Types of Secrets

1. **Generic Secrets (Opaque)**  
   These are used for storing general sensitive data. Users can define multiple key-value pairs.

2. **Container Registry Secrets (Docker Config)**  
   It is essential for pulling images from private container registries.

3. **TLS Secrets**  
   Stores SSL certificates and keys for secure communications.

4. **Key Vault Credentials Secrets**  
   Used for integrating with Azure KeyVault, holding the necessary access credentials.

5. **ArgoCD Repository secret**
   ArgoCD Repository secrets store the credentials needed to access the source repository for an ArgoCD project

#### Sample Secret Definitions

Each section below provides a sample definition of different OpenShift secrets, explaining their structure and usage.

##### Generic Secret (Opaque)
A generic OpenShift secret uses the type opaque. Here, you define as many key-value pairs as you would like. Below is a sample of the syntax:

=== "Secret"
    ```yaml
    apiVersion: v1
    kind: Secret
    metadata:
      name: <secret_name>
      namespace: <tenant>-<env>
    data:
      <key1>: <value1>          # Substitute key1 and vaule1 with your key-value pair. Encode vaule1 with base64
      <key2>: <value2>
    ```
=== "Demo example"
    ```yaml 
    apiVersion: v1
    kind: Secret
    metadata:
      name: mysecret
      namespace: demo-dev
    data:
      username: dXNlcm5hbWU=    # Decoded value: username
      password: cGFzc3dvcmQ=    # Decoded value: password
    ```

##### Container Registry Secret (Docker Config)

To pull images from a container registry, the namespace needs an `imagePullSecret`. This secret must be defined with the type `kubernetes.io/dockerconfigjson`. The key should be named `.dockerconfigjson`, and the value should follow this format: 

```
{"auths":{"<repoURL>":{"username":"<ClientID>","password":"<ClientSecret>"}}}
```

!!! Note
    Omitting the newline when encoding the value with base64 is crucial. Since this value includes the character `"`, use `'` when encoding the value with base64, e.g., `echo -n '<.dockerconfigjson>' | base64`.

=== "Secret"
    ```yaml
    apiVersion: v1
    kind: Secret
    metadata:
      name: <secret_name>
      namespace: <tenant>-<env>
    type: kubernetes.io/dockerconfigjson
    data:
      .dockerconfigjson: <json string containing repoURL, ClientID and ClientSecret>
    ```

=== "Demo example"
    ```yaml
    apiVersion: v1
    kind: Secret
    metadata:
      name: demo-docker-pull-secret
      namespace: demo-dev
    type: kubernetes.io/dockerconfigjson
    data:
      .dockerconfigjson: eyJhdXRocyI6eyJkZW1vLmF6dXJlY3IuaW8iOnsidXNlcm5hbWUiOiIzOTBmdWc5NC05M2o1LTNlcjYtOTI2My1kc2Y4OTJoYWtqZmUiLCJwYXNzd29yZCI6IlQ4ajhRfnFXcklhdndocHNIUmFSenMyYUpkSkphdnB1TGVKbHRkQkoifX19

    # Decoded value: {"auths":{"demo.azurecr.io":{"username":"390fug94-93j5-3er6-9263-dsf892hakjfe","password":"T8j8Q~qWrIavwhpsHRaRzs2aJdJJavpuLeJltdBJ"}}}
    ```

##### TLS certificate secrets
SSL/TLS secrets in OpenShift are used for storing certificates and keys to enable secure communications over networks. 

=== "Secret"
    ```yaml
    apiVersion: v1
    kind: Secret
    metadata:
      name: <secret_name>
      namespace: <tenant>-<env>
    type: kubernetes.io/tls
    data:
      tls.crt: <TLS_public_crt>
      tls.key: <tlS_private_key>
    ```

=== "Demo example"
    ```yaml
    apiVersion: v1
    kind: Secret
    metadata:
      name: ingress-certificate
      namespace: demo-dev
    type: kubernetes.io/tls
    data:
      tls.crt: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCg==...
      tls.key: LS0tLS1CRUdJTiBSU0EgUFJJVkFURSBLRVktLS0tLQ==...
    ```

##### Keyvault-credentials (Azure KeyVault)
KeyVault-credentials is a regular generic secret, but to standardize we use a default setup. Below is a sample of the syntax:

=== "Secret"
    ```yaml
    apiVersion: v1
    kind: Secret
    metadata:
      name: keyvault-credentials
      namespace: <tenant>-<env>
    data:
      ClientID: <App_Registration_Object_ID>
      ClientSecret: <Service_Principal_value>
    type: Opaque
    ```

=== "Demo example"
    ```yaml
    apiVersion: v1
    kind: Secret
    metadata:
      name: keyvault-credentials
      namespace: demo-dev
    data:
      ClientID: MzkwZnVnOTQtOTNqNS0zZXI2LTkyNjMtZHNmODkyaGFramZl               # Decoded value: 390fug94-93j5-3er6-9263-dsf892hakjfe
      ClientSecret: VDhqOFF+cVdySWF2d2hwc0hSYVJ6czJhSmRKSmF2cHVMZUpsdGRCSg==   # Decoded value: T8j8Q~qWrIavwhpsHRaRzs2aJdJJavpuLeJltdBJ
    type: Opaque
    ```

###### Repository credentials (ArgoCD repository)
To use a private repository as source repository for an ArgoCD application, it requires repository credentials. It is a generic secret, but for ArgoCD to notice it as a repository secret, it requires a specific label:

=== "Secret"
    ```yaml
    apiVersion: v1
    kind: Secret
    metadata:
      name: <tenant>-git-repository
      namespace: gitops-developers
      labels:
        argocd.argoproj.io/secret-type: repository
    data:
      username: <username>
      password: <password>
      project: <ArgoCD_project, e.g. tenant_name>
      type: <type, e.g. git>
      url: <repoURL>
    type: Opaque
    ```

=== "Demo example"
    ```yaml
    apiVersion: v1
    kind: Secret
    metadata:
      name: demo-dev-git-repository
      namespace: gitops-developers
      labels:
        argocd.argoproj.io/secret-type: repository
    data:
      username: dXNlcm5hbWU=    # Decoded value: username
      password: cGFzc3dvcmQ=    # Decoded value: password
      project: ZGVtbw==         # Decoded value: demo
      type: Z2l0                # Decoded value: git
      url: 
    type: Opaque
    ```
