# Setting Up a Private Helm Registry with Argo CD

In order to create a private helm registry there is a few configuration steps that need to be done. In the azure environment it needs to be configured an App Registration and an Azure Container Registry (ACR). In addition to these azure resources, there is a need to configure a sealed secret using kubeseal which can will be used to integrated Argo CD to pull the private helm registry from the ACR through the App Registration. 

## Configuring an App Registration

The App Registartion configuration for this process is very simple. If there is already an App Registration present in the azure environment it is possile to use that one, however, if it´s wanted to configure a new App Registration for the specific purpose of this setup it would be sufficiant to create a default App Registration with no application permissions.

How to configure an App Registration int the azure portal:

1. In the Azure portal, select Microsoft Entra ID
2. Select App registrations
3. Select New registration
4. For Supported account types, select Accounts in this organization directory only. Leave the other options as is
5. Select Register

In addition to configuring an App Registration we need to create a client secret for the App Registration, which can be done by following these steps:

1. In the App Registartion navigate to Manage -> Certificates & secrets
2. Select 'New client secret'
3. Choose an appropriate name and expiary date of the client secret
4. Select Add

Before moving on note down the Application ID (client ID) and the client secret just created. These will be needed for steps in a later section.

## Configuring an Azure Container Registry

The ACR configuration for this process is also very simple. If there is already an ACR present in the azure environment it is possile to use that one, however, if it´s wanted to it is possible to configure a new ACR for the specific purpose of this setup.

How to configure an Azure Container Registry in the azure portal:

1. Select Create a resource -> Containers -> Container Registry
2. In the Basics tab, enter values for Resource group, Registry name, Location and SKU type
3. Accept default values for the remaining settings. Then select Review + create. After reviewing the settings, select Create

In this setup we want tu use the App Registration to pull the private helm registry from the ACR. Therefore, we need to give the App Registration the sufficiant permissions to do so. This is configured through the ACR itself.

Configure pull permissions for the App Registration to the ACR:

1. In the ACR navigate to Access control (IAM) -> Add -> Add role assignment
2. In the Role tab search for and select the ArcPull role. Then select Members
3. In the Members tab, search for and select the App Registration. Then select Review and assign
4. After reviewing the assignment, select Review and assign again

## Configuring a Sealed Secret for integration with Argo CD

Now that we have configured an App Registration and an ACR with the needed permissions and noted down the different variables we need. We configure the sealed secret we need for the Argo CD integration.

In the tenant definition file we need to configure the gitops section. When empty, this is what section looks like:

``` yaml title="values.yaml"
  gitops:
    helm_registry:
      enable_custom_helm_registries: false
      enableOCI: "" # Global variable - decrypted value true for namespace gitops-developer - Encrypted and sat by cluster admins
      type: "" # Global variable - decrypted helm for namespace gitops-developer - Encrypted and sat by cluster admins
      helm_registries:
      - repository_name: "" # Encrypted secretname
        url: "" # Encrypted helm URL
        password: "" # Encrypted password
        username: "" # Encrypted username
```
