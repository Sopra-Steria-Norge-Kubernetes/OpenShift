# GitOps Setup

## Introduction
This page gives an introduction to how to setup OpenShift GitOps with your OpenShift Tenants and Git repository.

!!! Note
    Before you start, make sure you have a basic understanding of GitOps, ArgoCD, and OpenShift. This guide assumes familiarity with these technologies.


## GitOps Methods
CaaS provides two primary methods for deploying an synchronising infrastructures:

1. **Auto-defined ArgoCD applications:** A ArgoCD applicationSet automatically creates applications when resources are defined in the target path.
     1. Target path: `<basepath>applicationsets/<environments[].name>/*`
     2. OpenShift Tenant variable: `argocd.enable_auto_defined_apps`
   
2. **User-defined ArgoCD applications:** The user creates its own ArgoCD application definitions. Only recommended to use if more functionality needed than what is provided in the Auto-defined method. The ArgoCD application definition must be defined in the correct target path.
     1. Target path: `<basepath>applications/<environments[].name>/*`
     2. OpenShift Tenant variable: `argocd.enable_user_defined_apps`

## Pre-requisites
Before you can utilise the GitOps capabilties on OpenShift you need to setup your OpenShift Tenant correctly. In this repository, we will use a simple example tenant to enable the desired capabilties for both methods. the tenant definition is shown below. 
### Setting up a Tenant
Before we can start we must first define a Tenant and send it to our OCP administrators. Below is a minimal definition for setting up a tenant with two environments, `dev` and `test`, and enabling both GitOps methods above:
```yaml
appname: poseidon1
values: |
  namespace:
    name: poseidon1
    description: This is a test tenant named poseidon1 for showcasing the GitOps capabilities. 
    displayName: poseidon1, testing GitOps
    limits:
      memory: 2Gi
      cpu: 1

  environments:
    - name: dev
    - name: test

  rbac:
    ad_group_write_access: AZURE-AD-GROUP-WRITE-NAME
    ad_group_read_access: AZURE-AD-GROUP-READ-NAME

  argocd: 
    enable_auto_defined_apps: true
    enable_user_defined_apps: true
    main_git_repository:
      repourl: https://github.com/TeamPoseidonOCP/poseidon1_main_repo
      basepath: ""
      encrypted_url: <Encrypted repourl with sealedsecrets>
      encrypted_type: <value git encrypted>
      encrypted_password: <PAT always encrypted with sealedsecrets>
      encrypted_username: <Username used with PAT encrypted with sealedsecrets>
```

- Setting the variable `enable_auto_defined_apps` to true gives you the option to use the **Auto-defined ArgoCD applications** method for creating applications. 

- Setting the `enable_user_defined_apps`to true gives you the option to use the **User-defined ArgoCD applications** method for creating applications. 

!!! Info
    More information on how to connect ArgoCD to your repository can be found [here](../../../OpenShift%20Tenants/Tenant%20features/GitOps/gitops-introduction.md).

## Getting started
With your tenant configuration successfully completed, the next crucial step is establishing the structure in your main Git repository. 

This section will illustrate both the **auto-defined** and **user-defined** methods for managing applications with ArgoCD. We will use the `poseidon1_main_repository` folder as our example to walk you through the process. Check the example folder [here](https://github.com/Sopra-Steria-Norge-Kubernetes/OpenShift/tree/main/code-examples/ArgoCD-Tenant-setup/poseidon1_main_repo).

Consider this folder as your repository blueprint, with the basepath set to an empty string, providing a clear and applicable framework for your GitOps setup. Let´s get started! 

### Auto-defined ArgoCD applications
This approach is the recommended approach for working with ArgoCD. It gives an intuitive and easy way of working with multiple ArgoCD applications without implementing a nameingstandard across tenants.

#### How it works in the background
The ArgoCD applicationsets looks in the target path `poseidon1_main_repo/applicationsets/<environment_name>`, so every folder created in this path will create a application in ArgoCD. The name of the application will have the following name  `<tenant_name>-<environment_name>-apps-<folder_name>`. For instance if we create a folder called `applicationsets/dev/demo-kubernetes` it will be called `poseidon1-dev-apps-demo-kubernetes` in ArgoCD. 

In addition to the environments a base folder is created in the repository to easier share definitions across environments. The structure of the applicationsets will look like this: 

```bash
├── applicationsets
│   ├── base
│   ├── dev
│   └── test
```

#### Deployment Examples
Applications can be deployed with kustomization files, helm templates, helm repositories or plain kubernetes YAML definitions. The examples below show different methods for deploying resources to a cluster:

##### 1) Using YAML definitions of Kubernetes resources
Kubernetes resources can be deployed to the cluster by simpling adding files with YAML definition of Kubernetes resources. Folder `applicationsets/dev/ex1-kubernetes-resources` illustrates an example of how this is done for a service resource. 

##### 2) Kustomization file
The recommended way of provisioning infrastructure through ArgoCD is using a Kustomization file. A Kustomization file can be used to define multiple Kubernetes resoucres. It will only deploy the Kubernetes resources listed in the kustomization (`kustomization.yaml`) file like this:

```yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization

resources:
  - deployment.yaml
  - service.yaml
```

This will deploy `deployment.yaml` and `service.yaml` from the current folder. 

With Kustomize you can use yaml-files from a common base, and you can patch enviroment spesific values. Here's an example where files from a base (`../../base/application`) is used and the number of replicas for the Deloyment `app-deployment` is patched to `3`:

```yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization

resources:
- ../../base/application

patches:
- target:
    kind: Deployment
    name: app-deployment
  patch: |
    - op: replace
      path: /spec/replicas
      value: 3
```

Finally, you can leverage Git and its references to extract your base configuration into an external repository, allowing you to version control your configuration by utilizing branches and tags in Git.

```yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization

resources:
- https://<git remo url>//base/application?ref={<branch>,<tag>,<commit id>}

patches:
- target:
    kind: Deployment
    name: app-deployment
  patch: |
    - op: replace
      path: /spec/replicas
      value: 3
```

Use [Kustomize](https://kustomize.io/) cli to test your configuration.

##### 3) Helm 
Helm is another effective tool for deploying and managing Kubernetes applications. It simplifies the process of defining, installing, and upgrading even the most complex Kubernetes applications. Here we will list different examples for deploying helm:

1. Local Helm chart: If you want to define a helm chart locally folder `applicationsets/dev/ex3-helm-1` gives an example for how this can be done. Here a simple service and serviceaccount is defined under the folder `templates/` and the application is created by reading the `values.yaml` and `Chart.yaml` file.
   
2. External Helm repository: If you want to use an external Helm repository folder `applicationsets/dev/ex3-helm-2` gives an example of how this can be done through a kustomization file. Kustomize uses a helmCharts field which has the ability to use the helm command line program in a subprocess to inflate a helm chart, generating YAML as part of (or as the entirety of) a kustomize base. 
    1. This only works for public repositories. If you want to use a private repository it is recommended to create an ArgoCD application with the [user-defined method](#user-defined-argocd-applications). 
   
### User-defined ArgoCD applications

The user-defined method for creating ArgoCD applications is only recommended if the auto-defined methos don´t meet your applications requirements. The reason for this is that this method has some limitiations:

* Developers need to have more in-depth knowledge of ArgoCD
* It can be difficult to mange many ArgoCD applications in one Tenant
* A naming standard is required since the ArgoCD applications will have to be deployed in the namespace `gitops-developers`. This is a common namespace across all Tenants. Talk to your Tenant administrator regarding the naming standard.

When can it be useful to use user-defined ArgoCD applications:

* If you need to use a private helm repository
* If you want more freedom with your ArgoCD application definitions. 

The following file `poseidon1_main_repo/applications/dev/ex1-helm-app.yml` shows an example of how to deploy an application utilising a helm chart.

## Further Reading
Now that you understand better how to setup your CD pipeline on Openshift using ArgoCD, it can be useful to better understand how to use the tool. Check out [GitOps Best Practices](gitops-best-practices.md) for more information about how to use ArgoCD 



## Helpful Resources:
* [HelmChartInflationGenerator](https://kubectl.docs.kubernetes.io/references/kustomize/builtins/#_helmchartinflationgenerator_)
