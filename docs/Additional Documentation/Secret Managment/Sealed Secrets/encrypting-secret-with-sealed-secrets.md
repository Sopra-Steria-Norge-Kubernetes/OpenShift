---
Author: Oskar Marthinussen
Title: Encrypt secrets with Kubeseal
Version: 2.0.0
externally-exposed: true
--- 

# Encrypt secrets with kubeseal
This guide explains how to create a `SealedSecret` by encrypting an OpenShift Secret using Kubeseal. SealedSecrets are safe to store in version control and can only be decrypted by the controller running in the target OpenShift cluster.


## Pre-requisites
Before you begin, make sure you have:

* `Kubeseal` installed on your local machine. Follow this guide: [Install Kubeseal](../../Tools/install-kubeseal.md)
* The public certificate for your OpenShift cluster, available in the tenant repository under `/certificates`

## 1. Encode Your Secret

OpenShift secrets require base64-encoded values. To encode a string:

```
echo -n 'my-password' | base64
```

!!! tip
    Use the `-n` flag with echo to avoid appending a newline character, which would alter the result.


## 2. Create an OpenShift Secret Manifest

Construct a standard Secret manifest using your base64-encoded values. For example, if you're storing Azure credentials:

=== "Configuration Template"
    ```yaml
    apiVersion: v1
    kind: Secret
    metadata:
      name: <secret-name>
      namespace: <namespace>
    data:      
      ClientID: <client_id>
      ClientSecret: <client_secret>
    type: Opaque
    ```
=== "Example"
    ```yaml
    apiVersion: v1
    kind: Secret
    metadata:
      name: team-poseidon-gitops
      namespace: team-poseidon
    data:      
      ClientID: YWFkMmM4ZDItMTIzNC00YWJjLTllOGQtYWJjZGVmMTIzNDU2
      ClientSecret: ejN4OFF+QTdQOXRMa3FMSmpzOE4yYkMuM2NHfnJEOXVHUHVCbGJ6aw==
    type: Opaque
    ```

## 3. Encrypt Secret using Kubeseal

To generate a SealedSecret, run the following command:

```
kubeseal --cert /path/to/pub.crt --scope namespace-wide -o yaml < secret.yaml
```

Replace `/path/to/pub.crt` with the path to your cluster's public certificate.

This will output a SealedSecret manifest that is safe to commit to a Git repository.

!!! note
    The `--scope namespace-wide` flag restricts the sealed secret to the specified namespace, meaning it can only be decrypted within that namespace.