---
Author: Simen Haga
Title: Configuring Cluster External Secrets
Version: 1.0.0
externally-exposed: true
--- 

# Creating a ClusterExternalSecret

A `ClusterExternalSecret` is a type of secret that can be deployed across multiple environments and shared between multiple tenants in OpenShift. It enables centralized secret management using Azure Key Vault.

Customers do not have direct access to create `ClusterExternalSecret` objects in OpenShift, but Sopra Steria can create it on their behalf by following the steps below.

The customer will be responsible for managing the secret after it has been created by Sopra Steria as requested.

## Prerequisites

- A team or tenant must be defined in the cluster, with an associated ClusterSecretStore or SecretStore configured.

## Procedure

1. **Store the secret in Azure Key Vault**  
    - The secret must be stored in the team's Key Vault that is linked to their `SecretStore` or `ClusterSecretStore`.

2. **Provide the following information to Sopra Steria:**

    - Name of the `SecretStore`/`ClusterSecretStore` to be used
    - Desired name of the OpenShift secret to be created in the team's namespace
    - Name of the key in the resulting OpenShift secret
    - Name of the corresponding key in Azure Key Vault

Once this information is received, Sopra Steria will create the `ClusterExternalSecret` object for the customer.