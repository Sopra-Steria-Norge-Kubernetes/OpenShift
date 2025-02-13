# Using a Private Helm Registry

## Prerequsites

- Completed the steps in: [**Azure Configuration**](private-helm-registry-azure.md)
- Completed the steps in: [**OpenShift Tenant Configuration**](private-helm-registry-openshift-tenant.md)

## How to use a Private Helm Registry

This is a guide to showcase one of the methodes that can be used to utilize your private helm registry for your deployments in your tenant through applicationsets. In this example we will be using the following file structure:

![private-helm-reg-deployment-tree.png](../../../../img/Private%20Helm%20Registry/private-helm-reg-deployment-tree.png)

First we will define the helm registry we want to use. This is defined in our Chart.yaml, and contains the OCI link to the private helm registry, which helm registry and version we want to use. Below is how our Chart.yaml looks like this:

![private-helm-chart-yaml.png](../../../../img/Private%20Helm%20Registry/private-helm-chart-yaml.png)

Next up we need to define the resources we want to deploy. In this example we will deploy a service and a service account. We will define these resources to promote reuse, therefore these files will extract its content from a values file we define seperatly.

- Below is our Service implementation:

![developer_external_secret.png](../../../../img/Private%20Helm%20Registry/private-helm-template-svc.png)

- Below is out service account implementation:

![developer_external_secret.png](../../../../img/Private%20Helm%20Registry/private-helm-template-sa.png)

To get our resources deployed we now need to define the content of our resources before we push the code to version control, and as mentioned we will do this through a values.yaml file. below you can se our values.yaml file:

![developer_external_secret.png](../../../../img/Private%20Helm%20Registry/private-helm-values-yaml.png)