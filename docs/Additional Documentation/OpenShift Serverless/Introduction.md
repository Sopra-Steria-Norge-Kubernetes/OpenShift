# OpenShift Serverless Introduction

**Official Documentation:** [OpenShift Serverless](https://docs.openshift.com/container-platform/latest/serverless/about/about-serverless.html)

## What is OpenShift Serverless?

OpenShift Serverless enables deploying and managing serverless workloads on OpenShift. Built on **Kubernetes** and **Knative**, it provides scalable, event-driven applications without infrastructure management.

Focus on writing code while the platform automatically scales applications based on incoming requests, ensuring efficient resource utilization.

![Serverless Benefits](../../img/openshift%20serverless/serverless-benefit.png)

### Key Components:
- **Knative Serving**: Manages deployment, autoscaling, and networking for serverless applications.
- **Knative Eventing**: Facilitates event-driven architectures by routing and processing events.
- **Knative Functions**: Provides a streamlined way to build and deploy serverless functions.

## Why Use OpenShift Serverless?

Enables focusing on code development rather than infrastructure management. Simplifies deployment of microservices and serverless functions while leveraging Kubernetes' scalability, reliability, and security.

**Key Benefits:**
- **Automatic Scaling**: Applications scale dynamically based on traffic, including scale-to-zero when idle
- **Cost Efficiency**: Pay only for compute resources used, optimizing costs for sporadic workloads
- **Reduced Operational Overhead**: No server/VM management, faster development cycles
- **Microservices & Event-Driven**: Ideal for microservices architectures, API endpoints, and event-driven functions  

## Deployment Process

**1. Build Application Image**
Build your application into a container image with all necessary code and dependencies. Push the image to a container registry (Azure Container Registry, Docker Hub, etc.).

**2. Configure OpenShift Resources**
Create required OpenShift resources:
- **Network Policy**: Controls traffic flow
- **Service Account**: Manages permissions
- **Pull Secret**: Enables image registry access

**3. Deploy Knative Service**
Create and deploy the Knative service. OpenShift Serverless will automatically manage scaling and availability based on traffic.


## Further Reading
- [Building an OpenShift Serverless Image](Building-an-OpenShift-Serverless-Image.md)
- [Deploying a Knative Service with Tenant Helm Chart](quick-start-guide-with-tenant-chart.md)
- [Deploying a Knative Service without Tenant Helm Chart](quick-start-guide-without-tenant-chart.md)
