---
Title: External IP with MetalLB
---

# External IP with MetalLB

## Introduction

If you need to expose service from Openshift on a unique IP, [MetalLB](https://docs.openshift.com/container-platform/4.14/networking/metallb/metallb-configure-services.html) provides the possibility to create services with spesific IP adresses. 

The networking in and around Openshift is limited and restricted, so for practical use, discuss and plan the changes with Sopra Steria.

## Prerequisites

- A Openshift cluster with MetalLB set up
- An IP address pool provided by Sopra Steria Platform Team

## Plan

When adding a new external IP in Openshift there's some planning needed.

- Know where you need to reach your service from, and if it is exposed on to the Internet.
- Order the required network changes from Sopra Steria.

## User guide

Use a kubernetes Service object and add:

- spec.loadBalancerIP 
- metadata.annotations:
  metallb.universe.tf/address-pool: <address_pool_name>

### Example

The addresspool provided from Sopra Steria looks like this:

```yaml
apiVersion: metallb.io/v1beta1
kind: AddressPool
metadata:
  labels:
    app.kubernetes.io/instance: metallb
  name: dmz-allocated-network
  namespace: metallb-system
spec:
  addresses:
  - 10.0.0.200/29
  autoAssign: false
  protocol: layer2
```

Create a service that looks like this:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
  annotations:
    metallb.universe.tf/address-pool: dmz-allocated-network
spec:
  selector:
    app: my-app
  ports:
    - port: 8080
      targetPort: 8080
      protocol: TCP
  type: LoadBalancer
  loadBalancerIP: 10.0.0.201
```
