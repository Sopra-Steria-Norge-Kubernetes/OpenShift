---
Author: Oskar Marthinussen
Title: Encrypt secrets with Kubeseal
Version: 2.0.0
externally-exposed: true
--- 

# Encrypt secrets with kubeseal

This user guide provides an understanding of two different methods for encrypting secrets using `kubeseal`:

1. **External Encryption**: Encrypt secrets from outside the OpenShift cluster using a predefined public certificate.
2. **Internal Encryption**: Encrypt secrets directly within the OpenShift cluster by communicating with the sealed secret controller available inside.

## Common for both methods:

### 1. Encode Your Secret

Before encrypting, ensure that the data in your secret is base64-encoded. To encode a string to base64, use:

```bash
echo -n 'my-password' | base64
```

### 2. Construct an OpenShift Secret

Draft your secret with the base64-encoded data. Example:

```yaml
apiVersion: v1
kind: Secret
metadata:
  creationTimestamp: null
  name: <secret-name>
data:
  <key1>: <value1> # Substitute value1 with your base64-encoded string
  <key2>: <value2>
```

For ArgoCD repository credentials:

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: <secret-name>
  namespace: gitops-developers
  creationTimestamp: null
  labels:
    argocd.argoproj.io/secret-type: repository
data:
  type: <type> # Replace with your base64-encoded repository type (e.g., git)
  url: <repo_url> # Replace with your base64-encoded repository URL
  username: <username>
  password: <password>
type: Opaque
```

## External Encryption

Using external encryption, you'll need the public certificate from the sealed secrets controller.

### 1. Install Kubeseal

To encrypt secrets externally, you must have `kubeseal` installed on your local system:

```bash
wget https://github.com/bitnami-labs/sealed-secrets/releases/download/<release-tag>/kubeseal-<version>-linux-amd64.tar.gz
tar -xvzf kubeseal-<version>-linux-amd64.tar.gz kubeseal
sudo install -m 755 kubeseal /usr/local/bin/kubeseal
```

Replace `<release-tag>` and `<version>` with the appropriate version details.

### 2. Encrypt Secret using Kubeseal

Once `kubeseal` is installed, you can use it to seal your secrets:

```bash
kubeseal --cert /path/to/pub.crt --scope namespace-wide -o yaml < secret.yml > sealed_secret.yml
```

Replace `/path/to/pub.crt` with the appropriate path to your public certificate.

## Internal Encryption

Encrypting internally involves leveraging the Sealed Secrets controller that's already deployed within your OpenShift cluster.

### 1. Log into OpenShift Cluster

Ensure you're logged into the correct OpenShift cluster context.

### 2. Encrypt Secret with Kubeseal

Use the following command:

```bash
kubeseal --controller-name=sealed-secrets --controller-namespace=sealed-secrets --scope namespace-wide -o yaml < secret.yml > sealed_secret.yml
```

This command directly interacts with the Sealed Secrets controller in the cluster to seal your secret.

### Conclusion

Whichever method you choose largely depends on your setup, access rights, and use case. Both methods provide a secure way to store sensitive information and leverage the capabilities of `kubeseal` and Sealed Secrets within OpenShift.