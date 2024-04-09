---
Author: Simen Haga 
Title: Network communication on OpenShift 
Version: 1.0.0  
externally-exposed: true  
---


# Network communication on OpenShift

## Introduction
Red Hat OpenShift Networking is an ecosystem of features, plugins and advanced networking capabilities that extend Kubernetes networking with the advanced networking-related features that your cluster needs to manage its network traffic for one or multiple hybrid clusters. One of the key features of OpenShift is its ability to facilitate network communication between different components of an application, whether they are running on the same node or across multiple nodes in a distributed environment. With OpenShift, developers can easily configure network settings, manage network traffic, and ensure secure communication between different parts of their application.

## Connect OpenShift applications cross namespaces
OpenShift provides a powerful feature called namespaces, which allows different teams and applications to operate independently within the same OpenShift cluster. However, there may be situations where it is necessary to connect applications across different namespaces. For example, an application in one namespace may require access to a database or other service provided by an application running in a different namespace. In this case, developers can use the techniques mentioned in the user guide linked below to establish secure and reliable communication between the two applications.

* This [User guide](/SolidCloud/SolidCloud-Products/Containers/Red-Hat-OpenShift/User-Guides/5%2DNetwork-communication-on-OpenShift/5.1%2DConnect-OpenShift-applications-cross-namespaces) guide provides a guide for how to connect your OpenShift Application cross namespaces.

## Network observability
The Network Observability Operator is a critical component for developers looking to understand and monitor their network infrastructure within Kubernetes and OpenShift environments. This operator provides insights into network flows and helps maintain network visibility.

* This [User guide](../Observability/using-network-observability.md) will summarize how you can configure and use the network observability operator on your tenant.

## Exposing services on the internet.

Requirements:

* Your organization must allow developers to expose services on the internet.
* The label used to expose services on the internet for your organization.
* The wildcard domain used to expose services on the internet.
* An openshift tenant.

As a developer on Container as a service you are allowed to expose your applications on the internet, if your organization permits it. You will be provided a label that you can use for your ingress/route to define that it should be available on the internet.


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