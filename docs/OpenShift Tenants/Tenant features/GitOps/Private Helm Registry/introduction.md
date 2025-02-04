# Private Helm Registry for GitOps

## Why Private Helm Registeries
As a developer, you may need to use a private Helm chart to efficiently and securely deploy Kubernetes infrastructure. Private Helm registries store charts securely, ensuring version control, access management, and consistency across deployments. On an OpenShift platform, they enable reusable and parameterized templates for managing tenant environments. 

## How to setup a Private Helm Registry
For connecting to a Private Helm Registery you have to do the following:

1. [**Azure Configuration**](private-helm-registry-azure.md) - Setup Private container registry and make it accesiable for the GitOps controller (Argo CD)
    1. In this setup we will use a Azure Container Registery (ACR) and show how to configure it and to make it available for Argo CD
2. [**OpenShift Configuration**](private-helm-registry-openshift-tenant.md) - Add the ACR credentials to the OpenShift Tenant (or Team overlay)
    1. The credentials to access the Private helm registery are stored as a sealed secret through Git. The secret is encrypted and only accessible by the developer GitOps controller (Argo CD).