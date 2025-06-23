# Introduction Sealed Secrets

Sealed Secrets provide a secure and GitOps-friendly way to manage Kubernetes secrets in OpenShift by encrypting sensitive data so that it can safely be stored in Git. Unlike External Secrets, which fetch data from external providers like Azure Key Vault at runtime, Sealed Secrets rely on pre-encrypted content that is decrypted inside the cluster by the Sealed Secrets controller.

This method is useful when you don’t have access to a Key Management Service (KMS), or when you prefer to manage secret material directly within Git without integrating external backends.

The Sealed Secret controller runs inside the cluster and is the only component capable of decrypting sealed content into native Kubernetes `Secret` objects, ensuring your sensitive data is protected, even in version control.

## Key Benefits

- **GitOps-compatible** – Safe to store encrypted secrets in your Git repository alongside the rest of your manifests.
- **Cluster-side decryption** – Only the controller inside the OpenShift cluster can decrypt the data.
- **No external dependency** – Doesn’t require Key Vaults or cloud integrations.

## How It Works

1. You generate or obtain a Kubernetes secret (e.g. a Docker config, password, API key).
2. The secret is encrypted using the public certificate from the Sealed Secrets controller.
3. The encrypted SealedSecret resource is committed to Git.
4. When applied to the cluster, the Sealed Secrets controller decrypts it and creates a standard Kubernetes Secret.

## Encryption Scope

When encrypting secrets, you must define the encryption scope, which determines how specific the decrypted Secret can be:

- **Strict (default)**: The secret can only be decrypted in a specific namespace with a specific name.
- **Namespace-wide**: The secret must be created in a specific namespace, but it can have any name.
- **Cluster-wide**: Can be decrypted in any namespace and with any name. Use with caution.

!!! info
    This is specified when using `kubeseal` or the helper script provided in the tenant repository.

!!! warning
    Use `cluster-wide` scope with caution. This setting allows a sealed secret to be decrypted in any namespace, meaning that other users can potentially apply the secret in their own namespace and access its contents. This reduces namespace isolation and increases the risk of unintended secret exposure.

## Tooling and Workflow

We recommend using the provided helper script to simplify the encryption process when encrypting credentials for Key Vaults:

- `scripts/encrypt_client_credentials.sh`

Alternatively, you can follow the manual steps in our guide:

- [**Manually Encrypting with Sealed Secrets**](encrypting-secret-with-sealed-secrets.md)

## When to Use Sealed Secrets

Use Sealed Secrets if:

- You don’t use Azure Key Vault or another supported KMS
- You prefer full control of secret content in Git
- You want to manage secrets without external dependencies

For use cases involving shared credentials, automatic rotation, or large-scale secret reuse, we recommend [**External Secrets**](../External Secrets/Introduction.md) instead.