# Network Policy

## What is a Network Policy?

In OpenShift, network policies are specifications that define how pods within a namespace are allowed to communicate with each other and with other network endpoints. Network policies enable you to control the traffic flow at the IP address or port level, enhancing security and isolating different parts of your application.

## How to configure a Network Policy

We offer a few Network Policies from our Helm Chart. To configure a network policy within a namespace using our Helm chart, include the following YAML configuration in your Helm values file:

``` yaml
...
  networkPolicy:
    allow_same_namespace: true
    allow_from_openshift_monitoring: true
    allow_from_openshift_ingress: true
    allow_from_kube_apiserver_operator: true
...
```

## In-depth description of parameters

Each sub-component should have either a `true` or `false` value. The default value is set to `true` for each sub-component. 
In the table below, you can find a more detailed description of each variable in the `networkpolicy` feature:

| <div style="width:255px">**Variable**</div>                                 | **Description**                                           | **Example**                 | **Type**  | **Default Value**
|-----------------------------------------------|-----------------------------------------------------------|-----------------------------|-----------|------|
| `allow_same_namespace`          | Allows or disallows network communication within the same namespace | true        | Boolean   | true |
| `allow_from_openshift_monitoring` | Allows or disallows network communication to OpenShift monitoring | true         | Boolean   | true |
| `allow_from_openshift_ingress`  | Allows or disallows incoming network traffic from OpenShift Ingress controllers | true        | Boolean   | true |
| `allow_from_kube_apiserver_operator` | Allows or disallows communication to Kubernetes API Server Operator. Allowing communication from the operator ensures proper management and maintenance of the API server. | false         | Boolean   | true |
| `allow_from_grafana_operator`    | Allows or disallows communication to Grafana Operator      | true      | Boolean   | true |


## Further reading
- [Kubernetes Offical Documentation - Network Policies](https://kubernetes.io/docs/concepts/services-networking/network-policies/)
- [OpenShift Offical Documentation - Network Policies](https://docs.openshift.com/container-platform/4.13/networking/network_policy/about-network-policy.html)