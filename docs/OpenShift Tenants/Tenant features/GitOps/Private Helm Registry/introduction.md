# Introduction to Private Helm Registry

This solution effectively streamlines the deployment of tenants in an OpenShift cluster by leveraging a private Helm registry in an Azure Container Registry (ACR), retrieved via an Azure App Registration, and managed through Argo CD. The Helm charts stored in ACR serves as templates rather than static configurations. When a customer wants to deploy a new tenant, they specify the required resources and provide the necessary parameters, which are then applied to the Helm template. This allows for dynamic, customer-defined deployments while maintaining consistency and control.  

The process begins with the customer defining the specific configuration for their tenant, such as resource limits, networking details, or application-specific settings. These inputs are passed as Helm values, ensuring that each deployment is customized to the customer’s needs while still adhering to the structure defined in the Helm charts. Since the charts are stored in a private ACR, they remain secure and version-controlled, preventing unauthorized modifications or unintentional discrepancies. Authentication is handled via an Azure App Registration, which enables authorized services, such as Argo CD, to pull the required charts without exposing credentials.  

Argo CD plays a crucial role in the deployment pipeline by fetching the Helm templates from ACR and rendering them with the provided customer inputs. It then applies the generated Kubernetes manifests to the OpenShift cluster, ensuring that the tenant is deployed according to the specified requirements. Argo CD continuously monitors the deployed resources, automatically correcting any drift between the declared configuration and the actual state in the cluster. This approach ensures that deployments remain consistent and recoverable, with easy rollback capabilities in case of errors or required updates.  

This solution provides multiple advantages. Security is enhanced by keeping Helm charts in a private registry and using App Registration-based authentication. Automation through Argo CD reduces manual intervention while ensuring that deployments remain accurate and efficient. Scalability is significantly improved, as the use of templated Helm charts allows multiple tenants to be deployed dynamically without the need for extensive manual reconfiguration. The ability to specify parameters at deployment time adds flexibility, ensuring that customers can customize their deployments without compromising standardization. Additionally, version control within ACR enables tracking of changes and rollback options, further improving stability.  

To summarize, this approach provides a **secure, flexible, and automated** method for deploying and managing multi-tenant environments in OpenShift. By integrating Helm templates, Azure ACR, App Registration authentication, and Argo CD, it enables a **customer-driven, scalable, and reliable** deployment process that ensures both customization and consistency across all tenants.

## Configuration
To create a private helm registry there are a few configuration steps that need to be done. In the Azure environment we need to configure an App Registration and an Azure Container Registry (ACR). In addition to these Azure resources, we need to configure sealed secrets using kubeseal which will be used to integrate Argo CD, and make it able to pull helm templates from the private helm registry in an ACR through the App Registration.

Configure Private Helm Registry:

1. Configure an App Registration
2. Configure an Azure Container Registry
3. Give App Registration ArcPull permissions to the ACR
4. Create sealed secret for Client ID, Client Secret, ACR Login Server and ACR Name
5. Configure a new section in tenant definition values.yaml for GitOps

For more detailed instructions on how to set up the private helm registry visit: [Private Helm Registry Configuration](private-helm-registry-setup.md)