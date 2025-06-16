# Multiple Values Files


## User-Defined Applications

To use multiple values files with the user-defined Argo CD application method, your file structure should look something like the following:

![Basics Tab Configuration](../../img/Private%20Helm%20Registry/multiple-values-helm-app.png)

In your application code you need to specify the path to your common values file and environment specific values file. Below is an example:

```yaml title="helm-app.yml"
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: helm-demo-3-apps-of-apps-test
  namespace: gitops-developers # REMEMBER TO USE THE APPROPRIATE NAMESPACE HERE
spec:
  project: poseidon1
  sources:
    # value file repo
    # when using SSH key authentication the repo-url should be: 'git@github.com:<repo_path>'
    # when using PAT token authentication the repo-url should be: 'https://github.com/<repo_path>'
    - repoURL: git@github.com:TeamPoseidonOCP/poseidon1_main_repo.git
      targetRevision: HEAD
      ref: valuesfile
    # helm chart repo
    - repoURL: https://teamposeidonocp.github.io/helm-test-repo/
      chart: helm-test
      targetRevision: 0.0.1
      helm:
        releaseName: helm-test
        valueFiles:
          - $valuefile/applications/common_values/values_COMMON.yaml
          - $valuefile/applications/dev/environment_values-helm-app.yaml
  destination:
    server: "https://kubernetes.default.svc"
    namespace: poseidon1-dev
  syncPolicy:
    automated:
      selfHeal: true
      prune: true
      allowEmpty: true
```
[//]: # (TODO: modify & implement section under when we make it work)

<!-- ## Auto-Defined Applications

To use multiple values files with the auto-defined method we need to define a couple of files. In addition to our values files we need to define a Chart.yaml which defines the helm chart, and a kustomization.yml file which lets us define the helm chart we want to use as well as the values files. Your file structure should look somthing like this:

![Basics Tab Configuration](../img/Private%20Helm%20Registry/multiple-values-helm-app.png)

Below is an example of a Chart.yaml:

```yaml title="Chart.yaml"
apiVersion: v2
name: helm-testing
description: A Helm chart for testing
version: 0.1.0
appVersion: "1.16.0"
```

Below is an example of a kustomization.yml

```yaml title="kustomization.yml"
helmGlobals:
  chartHome: ../../charts # Relative path from kustomization to chart

helmCharts:
  - name: nginx
    valuesFile: values.yaml
``` -->


## Multiple Values Files - Private Helm Registry

When using private helm registries, it is possible to utilize multiple values file with the user-defined application method. However, Kustomize does not currently support the use of private registries which limits the use case for auto-defined applicationsets. To see how you can utilize multiple values files with the user-defined method visit: [**Using a Private Helm Registry**](../../OpenShift%20Tenants/Tenant%20features/GitOps/Private%20Helm%20Registry/private-helm-reg-usage.md)