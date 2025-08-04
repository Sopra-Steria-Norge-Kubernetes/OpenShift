---
Title: How to use EgressIP
---

# How to use EgressIP

## Routine

When using EgressIP there are some infrastructure changes needed, and these changes needs to be ordered by sending a request/change to Sopra Steria.

After the changes are in effect a EgressIP can be created. The EgressIP object looks something like this:

```yaml
apiVersion: k8s.ovn.org/v1
kind: EgressIP
metadata:
  name: egress-<tenant-name>-<app>
spec:
  egressIPs:
  - xxx.xxx.xxx.230
  podSelector:
    matchLabels:
      <foo>: <bar>
  namespaceSelector:
    matchLabels:
      soprasteria/tenant: <tenant-name>
```

Creating a EgressIP reqiures knowledge of the IP-range/Subnet and elevated cluster priviledges. State your needs and details in an order to Sopra Steria, and an administrator will process the order. 

### Firewall

Remember to update firewall according to the needs of you application(s).
