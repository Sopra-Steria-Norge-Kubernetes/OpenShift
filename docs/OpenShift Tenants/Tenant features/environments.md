# Environments

## What is an Environment?

In OpenShift, `environments` are specific configurations within a namespace designed to represent different stages of _development_, _testing_, or _production_. Each environment can have unique settings for internet access, external URLs, and IP addresses, ensuring that the resources within the environment are properly isolated and managed according to their specific needs.

## How to configure an Environment

To configure an environment within a namespace, include the following YAML configuration in your Helm values file:

```yaml
...
  environments:
    - name: <Name of environment 1 (e.g. dev)>
      allow_to_internet: <True or false - Set to true if environment should be exposed to internet>
      externalURLs:
        - <URL's that should be reachable from the environment (e.g. google.com)>
      externalIPs:
        - <IP adresses that should be reachable from environment in CIDR format (e.g. 10.218.0.0/24)>
...
```

By configuring these parameters, you can define multiple environments within a namespace, each tailored to the specific requirements of different stages in your development and deployment lifecycle.

## In-depth description of parameters

In the table below, you can find a more detailed description of each variable in the `environment` feature:

|  <div style="width:240px">**Variable**</div>                | **Description**                                                        | **Example**                | **Type** |
|------------------------------|------------------------------------------------------------------------|----------------------------|----------|
| `environments[].name`          | Name of environment                                                    | test                       | String   |
| `environments[].allow_to_internet`    | Set to `true` if environment should be exposed to internet. Default `false`. | `true`   | Boolean    |
|  `environments[].externalURLs` | A list of URLs that should be reached from a tenant environment | [ testurl.com, google.com] | List     |
| `environments[].externalIPs`    | A list of IP ranges that should be reached from the tenant environment | [0.0.0.0/0, 92.0.2.1/24]   | List     |