

# Egress Firewall


## Global Egress Firewall Openings

If there are some firewall openings that persist over multiple tenants  and environments in a cluster, these can be defined as global variables to reduce configuration overhead. These firewall openings are stored in a global variables file and is enabled in a tenant when the 'enable_global_egress_IPs' variable under 'namespace' is set to True. To enable this it is also required that the 'use_egress_firewall' variable is set to True. Below is an example of enabling global egress firewall openings:

```yml title="tenant.yml"
  namespace:
    name: ""
    description: ""
    displayName: ""
    use_egress_firewall: true # Enables egress firewall
    enable_global_egress_IPs: true # If set to true - global egress firewall openings are included in the egress firewall of the environments in the tenant
```

