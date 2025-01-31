# Introduction to Private Helm Registry

Text

## Configuration
In order to create a private helm registry there is a few configuration steps that need to be done. In the azure environment it needs to be configured an App Registration and an Azure Container Registry (ACR). In addition to these azure resources, there is a need to configure a sealed secret using kubeseal which can will be used to integrated Argo CD to pull the private helm registry from the ACR through the App Registration.

Configure Private Helm Registry:

1. Configure an App Registration
2. Configure an Azure Container Registry
3. Give App Registration ArcPull permissions to the ACR
4. Create sealed secret for Client ID, Client Secret, ACR Login Server and ACR Name
5. Configure a new section in tenant definition values.yaml for GitOps

For more detailed instructions on how to set up the private helm registry visit: [Private Helm Registry Configuration](private-helm-registry-setup.md)