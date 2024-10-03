# Introduction

## What is OpenShift Serverless?

OpenShift Serverless is a powerful extension of Red Hat OpenShift that allows developers to deploy and manage serverless workloads. Built on top of **Kubernetes** and **Knative**, it provides a way to build scalable, event-driven applications without the need to manage the underlying infrastructure.




With OpenShift Serverless, you can focus solely on writing code, while the platform automatically scales your applications up or down based on incoming requests. It ensures efficient resource utilization and simplifies operations, making it ideal for cloud-native, microservices-based architectures, and event-driven workloads.

![here](../img/openshift%20serverless/serverless-benefit.png)

### Key Components:
- **Knative Serving**: Manages deployment, autoscaling, and networking for serverless applications.
- **Knative Eventing**: Facilitates event-driven architectures by routing and processing events.
- **Knative Functions**: Provides a streamlined way to build and deploy serverless functions.

## Why Use OpenShift Serverless?

For developers, OpenShift Serverless enables focusing on code development rather than infrastructure management. It simplifies the deployment of microservices and serverless functions while seamlessly integrating with OpenShift, leveraging Kubernetes' scalability, reliability, and security features.


Key reasons to consider OpenShift Serverless:

- **Automatic Scaling**: Your applications scale dynamically based on traffic, even scaling down to zero when idle, conserving resources.  
- **Cost Efficiency**: Pay only for the compute resources you use, optimizing costs for workloads with sporadic or unpredictable traffic.  
- **Reduced Operational Overhead**: You don't have to worry about managing servers or VMs, speeding up your development cycles.  
- **Ideal for Microservices and Event-Driven Workloads**: Easily build microservices architectures, API endpoints, or event-driven functions that can respond to cloud events and external triggers.  

## How to Deploy a Serverless Application with OpenShift Serverless

To deploy a serverless application using OpenShift Serverless, there are several key steps to follow:

**1. Build Your Application Image**

The first step is to build your application into a container image. This image will contain all the necessary code and dependencies to run your serverless application. Once built, the image should be pushed to a container registry (e.g., Azure Container Registry, Docker Hub) where it can be accessed by OpenShift.

**2. Set Up OpenShift Resources**

Before deploying the application, several OpenShift resources need to be configured. This includes creating a **Network Policy**, a **Service Account**, and a **Pull Secret**. These resources are required to serve your Knative service.

**3. Deploy the Knative Service**

Once the image is in the container registry and the necessary resources are set up in OpenShift, the next step is to create and deploy the Knative service. After deployment, OpenShift Serverless will manage the scaling and availability of your application based on incoming traffic.


## Further Reading
-  [Building an OpenShift Serverless Image](Building-an-OpenShift-Serverless-Image.md)
-  [Deploying a Knative Service with Tenant Helm Chart](quick-start-guide-with-tenant-chart.md)
-  [Deploying a Knative Service without Tenant Helm Chart](quick-start-guide-without-tenant-chart.md)
