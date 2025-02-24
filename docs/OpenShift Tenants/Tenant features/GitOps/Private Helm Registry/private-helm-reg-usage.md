# Using a Private Helm Registry

## Prerequsites

- Completed the steps in: [**Azure Configuration**](private-helm-registry-azure.md)
- Completed the steps in: [**OpenShift Tenant Configuration**](private-helm-registry-openshift-tenant.md)

## Different methodes using a Private Helm Registry with Argo CD

There are different methodes availible to utilize a private helm registry with Argo CD. Below is a list containing the methodes we will cover in this section:

1. User-Defined Method
2. Auto-Defined Method

### User-Defined Method

#### Example

The user-defined method allows you to define Argo applications in a single file. This file contains all the nessesary information to create your app, such as name of the helm chart, url to the remote helm registry, and the version you want to utilize. In addition to this, you can modify and define specific fields in the helm chart outside of the default values. Below is an example of a user-defined argo application:

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: <tenant>-<applikasjon>-<miljø>
  namespace: gitops-developers # REMEMBER TO USE THE APPROPRIATE NAMESPACE HERE
spec:
  project: <virksomhet>-<tenant>
  source:
    chart: <name_private_helm_chart> # CHART NAME
    repoURL: <url_private_helm_registry> # YOUR ACR URL HERE
    targetRevision: 1.0.1 # CHART VERSION
    helm:
      releaseName: <name_private_helm_chart> # CHART NAME
  destination:
    server: "https://kubernetes.default.svc"
    namespace: <tenant>-<miljø>
  syncPolicy:
    automated:
      selfHeal: true
      prune: true
      allowEmpty: true
```

#### Multiple Values Files

This example, we will do almost the same as in the example above. However, in this example we will utilize multiple values files which allows you to easily define or overwrite the default or common values. 

Below is an example of how your file structure could look:

![Basics Tab Configuration](../../../../img/Private%20Helm%20Registry/helm-app-multiple-values.png)

Below is an example of the user-defined argo application:

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: <tenant>-<applikasjon>-<miljø>
  namespace: gitops-developers # REMEMBER TO USE THE APPROPRIATE NAMESPACE HERE
spec:
  project: <virksomhet>-<tenant> 
  sources:
    # value file repo
    - repoURL: <git-repo> # For SSH: git@github.com:<git-repo> --- For GitHub token: https://github.com/<git-repo>
      targetRevision: HEAD
      ref: valuesfile
    # private helm regsitry url
    - repoURL: <url_private_helm_registry> # YOUR ACR URL HERE
      targetRevision: 1.0.1 # CHART VERSION
      chart: <name_private_helm_chart> # CHART NAME
      helm:
        releaseName: <name_private_helm_chart> # CHART NAME
        valueFiles:
          - $valuesfile/applications/dev/values_COMMON.yaml       # Default values for your environments
          - $valuesfile/applications/dev/files/values_ENVIRONMENT.yaml  # Values you want to overwrite
  destination:
    server: "https://kubernetes.default.svc"
    namespace: <tenant>-<miljø>
  syncPolicy:
    automated:
      selfHeal: true
      prune: true
      allowEmpty: true
```

### Auto-Defined Method

We will cover an options that can be used to deploy Private Helm charts with ArgoCD ApplicationSets when choosing the auto-defined method. In our example we will be referencing the Helm chart directly from its Chart.yaml.

#### Example

This is a Chart.yaml file defining the private Helm registry. To use this, you need the registry name, OCI link, and version. Below is an example:

```yaml title="Chart.yaml"
apiVersion: v2
name: helm-testing
description: A Helm chart for testing
dependencies:
- name: <name_private_helm_chart> # CHART NAME
  version: 1.0.1 # CHART VERSION
  repository: oci://<url_private_helm_registry> # OCI link to private helm registry
version: 1.0.0
```

<!-- In addition to Chart.yaml, a values.yaml file needs to be defined to specify configurations and set the values in the Helm chart. We are in fact building the Helm chart in this repository and referencing its Chart and values file. See the examples in the [code examples](https://github.com/Sopra-Steria-Norge-Kubernetes/OpenShift/tree/main/code-examples/ArgoCD-Tenant-setup/poseidon1_main_repo/applicationsets/dev/ex3-helm-1) for more information. 

#### Example-2

This example, we will do almost the same as in example 1. However, in this example we will utilize multiple values files. Below is an example:

```yaml title="Chart.yaml"
apiVersion: v2
name: helm-testing
description: A Helm chart for testing
dependencies:
- name: nginx-helm-chart
  version: 1.0.1
  repository: oci://poseidon1.azurecr.io
version: 1.0.0
``` -->

<!-- This example uses a kustomization.yml file to define the Helm registry. You’ll need the registry name, OCI link, version, and values file. Below is an example:

```yaml title="kustomization.yml"
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
# Referencing a public repo outside the main repository

helmCharts:
  - name: helm-testing
    repo: oci://poseidon1.azurecr.io
    version: 1.0.1
    releaseName: nginx-helm-test
    namespace: poseidon1-dev
    valuesFile: values.yaml
```

In addition to Chart.yaml, a values.yaml file needs to be defined to specify configurations and change the helm charts default values to fit your environment and needs. See the examples in the [code examples](https://github.com/Sopra-Steria-Norge-Kubernetes/OpenShift/tree/main/code-examples/ArgoCD-Tenant-setup/poseidon1_main_repo/applicationsets/dev/ex3-helm-1) for more information.  -->

