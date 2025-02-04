# Private Helm Registry Azure Configuration

<!-- To create a private helm registry there are a few configuration steps that need to be done. In the Azure environment we need to configure an Azure Container Registry (ACR). To authenticate towards the ACR we have two methodes. The recommended method involves using an ACR access token and the ACR username for authentication. The second method would be to configure authentication through an App Registartion using the Application ID and an Application Secret. In addition to these Azure resources, we need to configure sealed secrets using kubeseal which will be used to integrate Argo CD. -->

When configuring a private helm registry with Argo CD, there are two different methodes that can be used for authentication. Our recommended approach would be using ACR access tokens:

- [ACR Access Token Authentication **(Method 1)**](#configuring-acr-access-token-authentication-method-1)
- [App Registration Authentication **(Method 2)**](#configuring-app-registration-authentication-method-2)

## Configuring an Azure Container Registry

Configuring Azure Container Registry:

1. Select Create a resource -> Containers -> Container Registry
![Basics Tab Configuration](../../../../img/Private%20Helm%20Registry/acr-step-1.png)
2. In the Basics tab, enter values for Resource group, Registry name, Location and SKU type
![Basics Tab Configuration](../../../../img/Private%20Helm%20Registry/acr-step-2.png)
3. Accept default values for the remaining settings. Then select Review + create. After reviewing the settings, select Create

Note down the ACR Login Server and the ACR Name.

### Configuring ACR Access Token Authentication **(Method 1)**

Configure ACR access token:

1. In the ACR navigate to Access control Settings -> Access Keys
2. Create a access token
3. Note down the username and access token

### Configuring App Registration Authentication **(Method 2)**

Configure an App Registration:

1. In the Azure portal, select Microsoft Entra ID
2. Select App registrations
![Basics Tab Configuration](../../../../img/Private%20Helm%20Registry/app-reg-step-2.png)
3. Select New registration
4. For Supported account types, select Accounts in this organization directory only. Leave the other options as is
![Basics Tab Configuration](../../../../img/Private%20Helm%20Registry/app-reg-step-4.png)
5. Select Register

Configure Client Secret for the App Registration:

1. In the App Registartion navigate to Manage -> Certificates & secrets
2. Select 'New client secret'
3. Choose an appropriate name and expiary date of the client secret
![Basics Tab Configuration](../../../../img/Private%20Helm%20Registry/app-reg-client-secret.png)
4. Select Add

Configure ArcPull permissions for the App Registration:

1. In the ACR navigate to Access control (IAM) -> Add -> Add role assignment
2. In the Role tab search for and select the ArcPull role. Then select Members
3. In the Members tab, search for and select the App Registration. Then select Review and assign
4. After reviewing the assignment, select Review and assign again

Note down the Application ID (client ID) and the Application Secret (Client Secret).

For the next step of configuring a sealed secret for integration with Argo CD: [Private Helm Registry OpenShift Tenant](private-helm-registry-openshift-tenant.md)