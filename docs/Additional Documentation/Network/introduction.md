---
Author: Simen Haga 
Title: Network communication on OpenShift 
Version: 1.0.0  
externally-exposed: true  
---


# Network communication on OpenShift

## Introduction
Red Hat OpenShift Networking extends Kubernetes with advanced features to manage network traffic across single or hybrid clusters. One of the key features of OpenShift is its ability to facilitate network communication between different components of an application, whether they are running on the same node or across multiple nodes in a distributed environment. With OpenShift, developers can easily configure network settings, manage network traffic, and ensure secure communication between different parts of their application.


## Connect OpenShift applications cross namespaces
Namespaces in OpenShift help isolate teams and applications within the same cluster. However, applications may sometimes need to communicate across namespaces—such as when one app relies on a service in another. In this case, developers can use the techniques mentioned in the user guide linked below to establish secure and reliable communication between the two applications.


* This [User guide](/SolidCloud/SolidCloud-Products/Containers/Red-Hat-OpenShift/User-Guides/5%2DNetwork-communication-on-OpenShift/5.1%2DConnect-OpenShift-applications-cross-namespaces) guide provides a guide for how to connect your OpenShift Application cross namespaces.

## Network observability
The Network Observability Operator is a component for developers looking to understand and monitor their network infrastructure within Kubernetes and OpenShift environments. This operator provides insights into network flows and helps maintain network visibility.

* This [User guide](../Observability/using-network-observability.md) will summarize how you can configure and use the network observability operator on your tenant.

## Exposing services on the internet.

Requirements:

* Your organization must allow developers to expose services on the internet.
* The label used to expose services on the internet for your organization.
* The wildcard domain used to expose services on the internet.
* An openshift tenant.

As a developer on Container as a Service, you may expose your applications to the internet if permitted by your organization. You’ll receive a label to apply to your ingress or route to enable external access.

```yaml title="Example"
kind: Ingress
apiVersion: networking.k8s.io/v1 (1)
metadata: 
  name: mynewroute (2)
  namespace: mytenant-dev (3)
  labels: 
    mydeveloperplatform/exposetointernet: "true" (4) 
spec:
  ingressClassName: openshift-ingress-external (5)
  rules:
  - host: grafana.external-test.cp.oslo.kommune.no (6) 
    http:
      paths:
      - backend:
          service:
            name: my-service (7)
            port:
              number: 3000 (8)
        path: /
        pathType: ImplementationSpecific
```

1. In the above example ingresses are used. You can also use the api for ingress if you prefer. If you wish to use the ingress API specify `apiVersion: route.openshift.io/v1` and `kind: Route`.
2. Name of your ingress object.
3. The namespace where your ingress recides.
4. The label used to define that your route should be exposed on the internet. Which label should be used for each customer is agreed upon once self-service of exposing services on the internet is approved.
5. The name of the ingress class you are using. Should be openshift-ingress-external. 
6. The URL of your exposed service.
7. The service you want to expose.
8. The port on which your service is exposed.

Once you have created your route/ingress object you may access your service on "your-subdomain"."Wildcard domain for exposed ingress".