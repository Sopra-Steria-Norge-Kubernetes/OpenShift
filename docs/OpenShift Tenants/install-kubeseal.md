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