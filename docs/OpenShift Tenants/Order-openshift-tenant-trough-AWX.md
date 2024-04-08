---
Author: Ersan Sinani
Title: Order OpenShift tenant
Version: 0.0.1
externally-exposed: false
--- 


# Ordering an OpenShift Tenant through AWX

This guide will walk you through the steps to order a new OpenShift Tenant using AWX. The process is straightforward, and you can modify the default settings after the tenant creation.

## Step 1: Tenant Details

**1. Navigate to the *Order new OpenShift Tenant* section in templates.**
![AWX templates](../img/Openshift%20Tenants/awx_templates.png)

**2. Fill in the required details:**
![AWX order tenant](../img/Openshift%20Tenants/awx_order_tenant.png)

Fill in the required details:

   - **Name of the tenant**: Enter a unique name for your OpenShift Tenant.
   - **Tenant CPU quota**: Enter the CPU quota for your tenant. Standard value: `4`.
   - **Tenant Memory quota**: Enter the memory quota in GB. Standard value: `16`.
   - **RBAC Azure AD Groups Write access**: Specify the Azure AD groups that should have write access.
   - **RBAC Azure AD groups Read access**: Specify the Azure AD groups that should have read access.
   - **ArgoCD Git Repository URL**: Enter the URL of the Git repository for ArgoCD.

## **Step 2: Additional Configuration**

   - **ArgoCD password for git repository**: Enter the password for the Git repository used by ArgoCD.
   - **ArgoCD username for git repository**: The default username is `admin`.
   - **Github app id**: If you're using a GitHub app, enter its ID.
   - **Github installation id**: Enter the GitHub installation ID if applicable.
   - **Github private key**: Provide the private key for the GitHub installation.

Click `Next` to proceed to the preview and confirmation step.

## Standard Configuration Values

After creating your tenant, the following standard values are set by default but can be changed as per your needs after the tenant is created by sopra steria:

```json title="json"
{
  "enable_tooling": true,
  "deploy_grafana": true,
  "take_backup": true,
  "backup_schedule": "0 0 * * *",
  "backup_ttl": "24h",
  "main_git_repository_type": "git"
}
```
##Conclusion
In conclusion, the user guide will assist you ordering an OpenShift tenant through AWX. It provides a clear, step-by-step process  to ensure a smooth and easy setup. The guide also outlines the standard values set for the tenant, which can be customized post-creation according to the user's requirements.