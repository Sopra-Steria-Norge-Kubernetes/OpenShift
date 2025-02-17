# Using a Private Helm Registry

## Prerequsites

- Completed the steps in: [**Azure Configuration**](private-helm-registry-azure.md)
- Completed the steps in: [**OpenShift Tenant Configuration**](private-helm-registry-openshift-tenant.md)

## Different methodes using a Private Helm Registry with Argo CD

There are different methodes availible to utilize a private helm registry with Argo CD. Below is a list containing the methodes we will cover in this section:

1. User-Defined Method
2. Auto-Defined Method

### User-Defined Method

The user-defined method allows you to define Argo applications in a single file. This file contains all the nessesary information to create your app, such as name of the helm chart, url to the remote helm registry, and the version you want to utilize. In addition to this, you can modify and define specific fields in the helm chart outside of the default values. Below is an example of a user-defined argo application:

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: helm-testing
  namespace: gitops-developers # REMEMBER TO USE THE APPROPRIATE NAMESPACE HERE
spec:
  project: poseidon1
  source:
    chart: nginx-helm-chart
    repoURL: poseidon1.azurecr.io
    targetRevision: 1.0.1
    helm:
      releaseName: nginx-helm-chart
  destination:
    server: "https://kubernetes.default.svc"
    namespace: gitops-developers
  syncPolicy:
    automated:
      selfHeal: true
      prune: true
      allowEmpty: true
```

### Auto-Defined Method

We will cover two options that can be used to deploy applicationsets when choosing the auto-defined method. These options will be presented as 'Demo-helm-1' and 'Demo-helm-2', where the main difference would be to use a 'Chart.yaml' or a 'kustomization.yml' to define the wanted helm registry.

#### Demo-helm-1

In this example we use a 'Chart.yaml' file to define the helm registry we want to use. To use this option you need the name of the helm registry we want to use, the OCI link to the remote private registry, and the version of the helm registry. Below is an example of a 'Chart.yaml' file for this use case:

```yaml title="Chart.yaml"
apiVersion: v2
name: helm-testing
description: A Helm chart for testing
dependencies:
- name: nginx-helm-chart
  version: 1.0.1
  repository: oci://poseidon1.azurecr.io
version: 1.0.0
```

In addition to the 'Chart.yaml', we can define a 'values.yaml' containing the wanted configuration and modification of the different features inside our helm registry. Below is an example of a 'values.yaml' file for this use case

```yaml title="values.yaml"
service:
  name: nginx-service
  type: ClusterIP
  port: 80
  targetPort: 9000
  selector:
    app: nginx

serviceAccount:
  name: "nginx-service-account"
  create: true
  annotations: {}
```

#### Demo-helm-2

In this example we use a 'kustomization.yml' file to define the helm registry we want to use. To use this option you need the name of the helm registry we want to use, the OCI link to the remote private registry, the version of the helm registry, and the values file we want to utilize. Below is an example of a 'kustomization.yml' file for this use case:

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

In addition to the 'kustomization.yml', we define 'values.yaml' much like in the 'Demo-helm-1' example. However, in this case we choose only to specify the name of a service. When doing this the deployment will give the service the specified name and use the default helm chart values for the rest of the spesification. Below is an example of a 'values.yaml' file for this use case

```yaml title="values.yaml"
service:
  name: helm-demo2-service
```

<!-- ![private-helm-reg-deployment-tree.png](../../../../img/Private%20Helm%20Registry/private-helm-reg-deployment-tree.png) -->
