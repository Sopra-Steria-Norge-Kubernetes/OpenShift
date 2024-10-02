# Deploying a Knative Service with Tenant Helm Chart

## Introduction

This guide explains how to deploy a Knative service in OpenShift using the Tenant Helm chart. By leveraging the Tenant Helm chart, all the necessary resources required to deploy a Knative service will be automatically created in your tenant. These resources include a **service account**, **network policy**, and **Docker pull secret**.


### Prerequisites

- **kubeseal**: Installed on your local machine for encrypting Docker credentials.
- **Service Account Credentials**: Ensure you have the credentials for the service account that will be used to pull the container image.
- **Container Image**: Your application has been built into a container image and pushed to a container registry (e.g., Docker Hub, Azure Container Registry, or Red Hat Quay).

---

## Steps to Deploy a Knative Service

### 1. Generate the Docker Config JSON

First, generate a Docker config JSON for your registry if you don’t have one already. You can do this using the following command:


=== "Command"
    ```bash
    oc create secret docker-registry openshift-serverless-pull-secret \
    --docker-server=<acr>.azurecr.io \
    --docker-username=<app_id> \
    --docker-password=<password> \
    --dry-run=client -o yaml | kubeseal \
    --controller-name sealed-secrets \
    --controller-namespace sealed-secrets \
    --scope cluster-wide -o yaml
    ```
=== "Example"
    ```bash
    oc create secret docker-registry demo-openshift-serverless-pull-secret \
    --docker-server=demo.azurecr.io \
    --docker-username=390fug94-93j5-3er6-9263-dsf892hakjfe \
    --docker-password=T8j8Q~qWrIavwhpsHRaRzs2aJdJJavpuLeJltdBJ \
    --dry-run=client -o yaml | kubeseal \
    --controller-name sealed-secrets \
    --controller-namespace sealed-secrets \
    --scope cluster-wide -o yaml
    ```

Replace the `<acr>`, `<app_id>`, and `<password>` placeholders with your actual credentials.


### 2. Add the Encrypted Docker Config to Helm Chart

In your Helm chart values file, populate the `openshift_serverless.dockerconfigjson` field with the encrypted Docker config from the previous step.

=== "Helm Chart Configuration"
    ```yaml
    openshift_serverless:
      dockerconfigjson: <encrypted-dockerconfigjson>
    ```
=== "Example"
    ```yaml
    openshift_serverless:
      dockerconfigjson: AgCpPP5NfOs4vmTKooXs0rEgCImPiK/gB2mlvMDdhCcyqPIsvvxCWGSGN1dXZMopg5MaCbm2yyD2cSU+5ZlcbodUYAqjJ+xo1b8QPHZTgSNi4b4//iItSGVICeShD4nErfJLnQ8wcL9+kksvj8B/pgBYcakkCPeGx8JYIoCtDZ2D4pa1iexZEQbk68Npw0OSzpoV5/bE+QP8NKLPcRlPngPBjBxuaZG6O2Nk1m5VCwIC4chQ9/l1d2w+VYKSxnak9XWyYSNwFrXFk2gOQdEbvQZDyik4g+fRYCcsQmGoQT3MM/XfaMFzxG5E11K4Jd0YdOzqV66c1bLYBOLzNoLV2IHNFdSFV8Sne87b5/1LnDIfEeqlGs1v/Hgfo4tY/6jcIsg1ITsPOXTOikgy4wVgNGmq6N2bdkwVsnx1OW1H8JnB25q3+4pcy1bK8Y4xfq/KROD8OHUhW/DU9u+AVs0LH7z7yliSLijIB0QliOKMDdeyAqrJ2DaF1l2uNujA0WpFC7WqDqJdKfgdc8SLxrNaMCnTmGeUpZC7geNbf9D/7ceP4VewNNrknTzrxLW2JFy+9FWyKVCidwP34j3vTgyxQSeT7kqLxZBJfgTKmUe6w1aB09baQxNBLYsQSBP9Kz7BweRyyJrVOTZTMjx51I7DHftVRMON9PEsobgxy+NBaR1l9sOp+ww/17uCIsEayNTTsCZu/uTbEb8VEwjeTDxA5kjXnONCOGy28R8NbGItoy/wUAVo4ItvFWvvGOQ/Usz0mgDYlPIH2spx1c5QTBTu6yrHRx4G0Sausjc/TviJWCrUgeFMJfdB9UMGGyv0cCtDeReV7cFIQqM=
    ```


### 3. Deploy resources to Your Tenant

Create a pull request with your given credentials. Once it's approved, all required resources will be deployed to your tenant.


### 4. Deploy the Knative Service

Once the resources are created in your tenant, you can deploy your Knative service. Here's an example of a Knative service YAML manifest:


=== "Knative Service Definition"
    ```yaml
    apiVersion: serving.knative.dev/v1
    kind: Service
    metadata:
      name: <service_name>
      namespace: <namespace>
    spec:
      template:
        spec:
          serviceAccountName: <service_account>
          containers:
          - image:  <acr>.azurecr.io/<repository>:<tag>
            livenessProbe:
              httpGet:
                path: /healthz
                port: 8080
              initialDelaySeconds: 5
              periodSeconds: 10
            readinessProbe:
              httpGet:
                path: /ready
                port: 8080
              initialDelaySeconds: 5
              periodSeconds: 10
    ```
=== "Example"
    ```yaml
    apiVersion: serving.knative.dev/v1
    kind: Service
    metadata:
      name: hello-world
      namespace: demo-dev
    spec:
      template:
        spec:
          serviceAccountName: demo-knative-sa
          containers:
          - image:  demo.azurecr.io/hello-world:latest
            livenessProbe:
              httpGet:
                path: /healthz
                port: 8080
              initialDelaySeconds: 5
              periodSeconds: 10
            readinessProbe:
              httpGet:
                path: /ready
                port: 8080
              initialDelaySeconds: 5
              periodSeconds: 10
    ```

### 5. Check the Status of the Service
Once deployed, you can check the status of the Knative service using the following command. This will give you detailed information on whether the service is running and any issues it might be encountering.

=== "Check status"
    ```bash
    kn service describe <service_name> -n <namespace>
    ```
=== "Example"
    ```bash
    kn service describe hello-world -n demo-dev
    ```

After deployment, you can retrieve the external URL where your service is exposed by running:

=== "Get route"
    ```bash
    oc get ksvc -n <namespace>
    ```
=== "Example"
    ```bash
    oc get ksvc -n demo
    ```

## Conclusion

This guide demonstrates how to use the Tenant Helm chart to automatically set up the resources required to deploy a Knative service in OpenShift. 
