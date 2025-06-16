# Deploying a Knative Service without Tenant Helm Chart

## Introduction

This quick start guide will walk you through deploying a Knative service in OpenShift. By the end of this guide, you will have a Knative service running that automatically scales based on traffic.

## Prerequisites

- **kubeseal**: Installed on your local machine for encrypting Docker credentials.
- **Service Account Credentials**: Ensure you have the credentials for the service account that will be used to pull the container image.
- **Container Image**: Your application has been built into a container image and pushed to a container registry (e.g., Docker Hub, Azure Container Registry, or Red Hat Quay).

---

## Steps to Deploy a Knative Service

### 1. Create a Network Policy
This step ensures that your Knative service can receive traffic from the OpenShift Serverless system.

=== "Create a Network Policy"
    ```yaml
    apiVersion: networking.k8s.io/v1
    kind: NetworkPolicy
    metadata:
      name: allow-from-serverless
      namespace: <namespace>
    spec:
      ingress:
      - from:
        - namespaceSelector:
            matchLabels:
              knative.openshift.io/system-namespace: "true"
      podSelector: {}
      policyTypes:
        - Ingress
    ```
=== "Example"
    ```yaml
    apiVersion: networking.k8s.io/v1
    kind: NetworkPolicy
    metadata:
      name: allow-from-serverless
      namespace: demo-dev
    spec:
      ingress:
      - from:
        - namespaceSelector:
            matchLabels:
              knative.openshift.io/system-namespace: "true"
      podSelector: {}
      policyTypes:
        - Ingress
    ```

### 2. Create a Pull Secret
This pull secret allows OpenShift to access your private image registry to pull the container image for your Knative service. You can either create it declaratively and imperativly:

#### a. Declaratively:
=== "Create a Pull Secret"
    ```yaml
    apiVersion: v1
    kind: Secret
    metadata:
      name: openshift-serverless-pull-secret
      namespace: <namespace>
    type: kubernetes.io/dockerconfigjson
    data:
      .dockerconfigjson: <base64-encoded-pull-secret>
    ```
=== "Example"
    ```yaml
    apiVersion: v1
    kind: Secret
    metadata:
      name: demo-openshift-serverless-pull-secret
      namespace: demo-dev
    type: kubernetes.io/dockerconfigjson
    data:
      .dockerconfigjson: AgCpPP5NfOs4vmTKooXs0rEgCImPiK/gB2mlvMDdhCcyqPIsvvxCWGSGN1dXZMopg5MaCbm2yyD2cSU+5ZlcbodUYAqjJ+xo1b8QPHZTgSNi4b4//iItSGVICeShD4nErfJLnQ8wcL9+kksvj8B/pgBYcakkCPeGx8JYIoCtDZ2D4pa1iexZEQbk68Npw0OSzpoV5/bE+QP8NKLPcRlPngPBjBxuaZG6O2Nk1m5VCwIC4chQ9/l1d2w+VYKSxnak9XWyYSNwFrXFk2gOQdEbvQZDyik4g+fRYCcsQmGoQT3MM/XfaMFzxG5E11K4Jd0YdOzqV66c1bLYBOLzNoLV2IHNFdSFV8Sne87b5/1LnDIfEeqlGs1v/Hgfo4tY/6jcIsg1ITsPOXTOikgy4wVgNGmq6N2bdkwVsnx1OW1H8JnB25q3+4pcy1bK8Y4xfq/KROD8OHUhW/DU9u+AVs0LH7z7yliSLijIB0QliOKMDdeyAqrJ2DaF1l2uNujA0WpFC7WqDqJdKfgdc8SLxrNaMCnTmGeUpZC7geNbf9D/7ceP4VewNNrknTzrxLW2JFy+9FWyKVCidwP34j3vTgyxQSeT7kqLxZBJfgTKmUe6w1aB09baQxNBLYsQSBP9Kz7BweRyyJrVOTZTMjx51I7DHftVRMON9PEsobgxy+NBaR1l9sOp+ww/17uCIsEayNTTsCZu/uTbEb8VEwjeTDxA5kjXnONCOGy28R8NbGItoy/wUAVo4ItvFWvvGOQ/Usz0mgDYlPIH2spx1c5QTBTu6yrHRx4G0Sausjc/TviJWCrUgeFMJfdB9UMGGyv0cCtDeReV7cFIQqM=
    ```
To seal the secret, see the **Secret Management/Encrypt secrets** documentation.

#### b. Imperativly:
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



### 3. Create a Service Account
The service account is configured with the pull secret, allowing it to authenticate with the private image registry.

=== "Create a Service Account"
    ```yaml
    apiVersion: v1
    kind: ServiceAccount
    metadata:
      name: openshift-knative-sa
      namespace: <namespace>
    imagePullSecrets:
      - name: openshift-serverless-pull-secret
    ```
=== "Example"
    ```yaml
    apiVersion: v1
    kind: ServiceAccount
    metadata:
      name: demo-knative-sa
      namespace: demo-dev
    imagePullSecrets:
      - name: demo-openshift-serverless-pull-secret
    ```

### 4. Deploy the Knative Service
This YAML file defines the Knative service and ties it to the service account you created earlier. Replace the `<acr>` and `<namespace>` placeholders with your actual Azure Container Registry (ACR) and namespace details.

=== "Deploy Knative Service"
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
          - image: <acr>.azurecr.io/<repository>:<tag>
            ports:
            - containerPort: 8080
    ```
=== "Example"
    ```yaml
    apiVersion: serving.knative.dev/v1
    kind: Service
    metadata:
      name: my-service
      namespace: demo-dev
    spec:
      template:
        spec:
          serviceAccountName: demo-knative-sa
          containers:
          - image: demo.azurecr.io/hello-world:latest
            ports:
            - containerPort: 8080
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

Your Knative service will now automatically scale based on demand, scaling down to zero when idle to conserve resources and scaling up to meet incoming traffic.
