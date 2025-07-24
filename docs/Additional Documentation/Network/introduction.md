---
Author: Simen Haga 
Title: Network communication on OpenShift 
Version: 1.0.0  
externally-exposed: true  
---

# Network Communication

Network traffic management and communication between OpenShift applications.

**Official Documentation:** [OpenShift Networking](https://docs.openshift.com/container-platform/latest/networking/understanding-networking.html)

## Cross-Namespace Communication

Applications may need to communicate across namespaces. Use secure communication techniques to connect applications in different namespaces.

**Guide:** [Connect OpenShift Applications Cross Namespaces](connect-openshift-application-cross-namespaces.md)

## Network Observability

Monitor and understand network infrastructure within OpenShift environments using the Network Observability Operator.

**Guide:** [Using Network Observability](../Observability/using-network-observability.md)

## Exposing Services to Internet

**Requirements:**

  - Organization approval for internet exposure
  - Specific label for external access
  - Wildcard domain for exposed services
  - OpenShift tenant

Apply the provided label to your ingress or route to enable external access.

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