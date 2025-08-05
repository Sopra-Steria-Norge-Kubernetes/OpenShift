# Introduction Sealed Secrets

Sealed Secrets provide a secure and GitOps-friendly way to manage Kubernetes secrets in OpenShift by encrypting sensitive data so that it can safely be stored in Git. Unlike External Secrets, which fetch data from external providers like Azure Key Vault at runtime, Sealed Secrets rely on pre-encrypted content that is decrypted inside the cluster by the Sealed Secrets controller.

This method is useful when you don’t have access to a Key Management Service (KMS), or when you prefer to manage secret material directly within Git without integrating external backends.

The Sealed Secret controller runs inside the cluster and is the only component capable of decrypting sealed content into native Kubernetes `Secret` objects, ensuring your sensitive data is protected, even in version control.


**Official Documentation:** [Sealed Secrets](https://github.com/bitnami-labs/sealed-secrets/)

## Key Benefits

- **GitOps-compatible** – Safe to store encrypted secrets in your Git repository alongside the rest of your manifests.
- **Cluster-side decryption** – Only the controller inside the OpenShift cluster can decrypt the data.
- **No external dependency** – Doesn’t require Key Vaults or cloud integrations.

## How It Works

1. Generate or obtain Kubernetes secret (Docker config, password, API key)
2. Encrypt secret using public certificate from Sealed Secrets controller
3. Commit encrypted SealedSecret resource to Git
4. Controller decrypts and creates standard Kubernetes Secret when applied

## Encryption Scope

When encrypting secrets, you must define the encryption scope, which determines how specific the decrypted Secret can be:

- **Strict (default)**: Only decrypted in specific namespace with specific name
- **Namespace-wide**: Specific namespace, any name
- **Cluster-wide**: Any namespace and name (use with caution)

!!! info
    Specified when using `kubeseal` or helper script from tenant repository.

!!! warning
    **Cluster-wide scope** allows decryption in any namespace, reducing isolation and increasing exposure risk.

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