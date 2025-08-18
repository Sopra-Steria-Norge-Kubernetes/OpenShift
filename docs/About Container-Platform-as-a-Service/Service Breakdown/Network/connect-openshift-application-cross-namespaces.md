---
Author: Simen Haga
Title: Connect OpenShift applications cross namespaces
Version: 0.1.0
externally-exposed: false
--- 

# Connect OpenShift applications cross namespaces

## Introduction
This user guide provides a guide for how to connect your OpenShift Application cross namespaces. This can be achieved be creating a network policy that permits incoming traffic from a specific external namespace to certain services within the target namespace. The policy is constructed so that all pods in the external namespace are granted access to the given service. Within the target namespace, the service that can be accessed through this policy has the selector `app=label`. In essence it's a access control allowing only specific pods from an external namespace to communicate with a given service in the current namespace.

## Prerequisites

- A pod and a service running in the target namespace with target ports set
- A second external namespace with a running application or pod

## User guide

### How to use
1. Replace `<namespace>` in the metadata section with the name of the namespace you want apply the policy to.
2. Replace `<namespace>` in the ingress rule with the namespace that you want to allow inbound traffic from.
3. Replace `<Label>` in the podSelector section with the label of the pods/service in the current namespace that you want the other namespace to access. E.g `app=httpd`
4. Apply the policy by running: 
```bash
oc apply -f networkpolicy.yml
```
5. Confirm your new networkpolicy by running 
```bash
oc get networkpolicy
```
6. From your external application/pod, try to curl the service in the target namespace on `IP:Port`


### Example
```yaml title="networkpolicy.yml"
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-from-other-namespace
  namespace: <namespace> # Target namespace
spec:
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          kubernetes.io/metadata.name: <namespace> # External namespace
  podSelector:
    matchLabels:
      app: <Label> # The label your service/pod have that the external namespace can access
  policyTypes:
  - Ingress
```
