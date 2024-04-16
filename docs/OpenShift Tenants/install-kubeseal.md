---
Author: Simen Haga, Marcus Notø
Title: Install Kubeseal
Version: 1.0.0
externally-exposed: true
--- 


# Kubeseal

## Introduction
This user guide provides detailed instructions and templates for setting up encrypted variables for the ArgoCD feature within the OpenShift Tenant framework. It is divided into Two main sections:

1. **Installation of Kubeseal**: This section guides you through installing Kubeseal, a tool essential for encrypting the variables.

2. **Encrypt Secret with Kubeseal**: This part explains how to use Kubeseal to seal a secret


## Install KubeSeal

If not already installed, you can install `kubeseal` on your machine with the following commands:

```bash
wget https://github.com/bitnami-labs/sealed-secrets/releases/download/<release-tag>/kubeseal-<version>-linux-amd64.tar.gz
```
```bash
tar -xvzf kubeseal-<version>-linux-amd64.tar.gz kubeseal
```
```bash
sudo install -m 755 kubeseal /usr/local/bin/kubeseal
```

Remember to replace `<release-tag>` and `<version>` with the latest release version. For example:

```bash
wget https://github.com/bitnami-labs/sealed-secrets/releases/download/v0.22.0/kubeseal-0.22.0-linux-amd64.tar.gz
tar -xvzf kubeseal-0.22.0-linux-amd64.tar.gz kubeseal
sudo install -m 755 kubeseal /usr/local/bin/kubeseal
```

For Mac users you can use Homebrew package manager:

```bash
brew install kubeseal
```

## Encrypt Secret with Kubeseal

To seal the secret with `kubeseal`, you can use the following command:

```bash
kubeseal --cert /path/to/pub.cert --scope namespace-wide -f secret.yaml -o yaml > seald_secret.yaml
```
Replace `/path/to/pub.cert` with the path to your public certificate provided by Sopra Steria. `secret.yaml` is the path to the Secret YAML file you created in the first step. The sealed secret will be outputted to `sealed_secret.yaml`.

=== "Secret"

    ```yaml 
    apiVersion: v1
    kind: Secret
    metadata:
      name: poseidon1
      namespace: gitops-developers
      labels:
        argocd.argoproj.io/secret-type: repository
    type: Opaque
    stringData:
      type: git
      url: https://github.com/TeamPoseidon/poseidon1_fake_main_repo.git
      username: team.poseidon
      password: pat_8VUndBHIJQCWIJUkhHWKWMFDAGAGMAGJ202ZIaT
    ```

=== "Sealed Secret"

    ```yaml 
    apiVersion: bitnami.com/v1alpha1
    kind: SealedSecret
    metadata:
      annotations:
        sealedsecrets.bitnami.com/namespace-wide: "true"
      creationTimestamp: null
      name: poseidon1
      namespace: gitops-developers
    spec:
      encryptedData:
        password: AgAKW3TQw5l51HOdRbuDjXpJvv+cm6AdE5E95kNn9yr9sifywF6VMSkToJO/YVs9w5NsG3Qd8CXR1dSB+klb3kJe9lIucY1gVML82XOZL+nRpVrDivyKNpxHfQOI0BOOg+wbQlZpzdhYaUUAkNN9vukAWoeb67BmDClMrhdzaVH19Htvc0GkoCNM/2HPbuKM8BTUn7xeUut6PVMmcNCKyb3uae3w26i8ElmQI10OSOMoFjVm37HNj1QnjbEmUmsvS6H0sVKo3/NiYC6T75FsesG/jp/FyNBDyjRlqpdSlJ0YYv15oEwI46ByxDFHyFA
        type: AgAjVZbAT7lXfEwsRC25vNqceFiDoiCLM/lUx4I7ti1ngfRG50MC8c5v3ZiEaCxTICBqkBJpGKtXcgih8jddhXsldWyKlwVFKP5z43LLv2nqNc5Hb/tKZFCP+OVXa/zeWbVREKm/+dH170t0RlTd9PtUJ947i3I5/uJSlayG+D+zg4XBxj2QfKmKS5hntfE
        url: AgCTo2lVv2zzReuviCxUlkfSF8BTNYX4iQAlQMP7D52AMpLa4fHJ/Ml+N3vzrmdsjeuOp2Wa51q9mz57tVKmYLHA7UQ7Jr2kwwTBNaiz2ZC7oeI2pLPtYuvhkKdz5vMBQxNzAmSavq7jdFulx9Q5k1BY3WNH3TYiQgwhXwHmIJKQS8gZH0JHU62UIZ7RzSNSxmqoejhurDIuzxdpm+Llm21U+VdHGBY+D7FnonEZ5xe5Hx9WayqYgFE
        username: AgCrjo7uFTS2TWJlE7z6bi2bU1Z2GHI8ycF9Ktg7ZSHMJUemC5Zo9kBD393ixQkElda+UnkU5vYT5LaGf6KiggI1y0Ww3LOjunc60VXy3C7jDH4qmlydR/J7qALCrxvqoB1aPWVEadFWLyMLhEqWhtqtV+AugZIzFt4BjL5y5jjl9IgvOlINp8/2HbNWqvn2E02wsMn
      template:
        metadata:
          annotations:
            sealedsecrets.bitnami.com/namespace-wide: "true"
          creationTimestamp: null
          labels:
            argocd.argoproj.io/secret-type: repository
          name: poseidon1
          namespace: gitops-developers
        type: Opaque
    ```