# Private Helm Registry for GitOps

## Why Private Helm Registeries
As a developer, you may need to use a private Helm chart to efficiently and securely deploy Kubernetes infrastructure. Private Helm registries store charts securely, ensuring version control, access management, and consistency across deployments. On an OpenShift platform, they enable reusable and parameterized templates for managing tenant environments. 

## How to setup a Private Helm Registry
For connecting to a Private Helm Registery you have to do the following:
1. Setup Private container registry and make it accesiable for the GitOps controller (Argo CD)
   1. In this setup we will use a Azure Container Registery (ACR) and show how to configure it and to make it available for Argo CD
2. Add the ACR credentials to the OpenShift Tenant (or Team overlay)
   1. The credentials to access the Private helm registery are stored as a sealed secret through Git. The secret is encrypted and only accessible by the developer GitOps controller (Argo CD).



## Private Helm Registry Configuration 
To create a private helm registry there are a few configuration steps that need to be done. In the Azure environment we need to configure an App Registration and an Azure Container Registry (ACR). In addition to these Azure resources, we need to configure sealed secrets using kubeseal which will be used to integrate Argo CD, and make it able to pull helm templates from the private helm registry in an ACR through the App Registration.

Configure Private Helm Registry:

1. Configure an App Registration
2. Configure an Azure Container Registry
3. Give App Registration ArcPull permissions to the ACR
4. Create sealed secret for Client ID, Client Secret, ACR Login Server and ACR Name
5. Configure a new section in tenant definition values.yaml for GitOps

For more detailed instructions on how to set up the private helm registry visit: [Private Helm Registry Configuration](private-helm-registry-setup.md)


## OpenShift Tenant configuration
 