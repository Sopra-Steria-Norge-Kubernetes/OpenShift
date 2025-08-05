# Team Network Policies

## Overview

Team namespaces are secured by default using Kubernetes Network Policies that implement a "default deny-all" approach with specific allow rules for essential services. This ensures that only authorized traffic can flow into and out of your team namespace.

## Default Security Posture

### Secure by Default
- **Default deny-all**: All ingress and egress traffic is blocked by default
- **Automatic allow rules**: Essential OpenShift services are automatically permitted
- **Configurable**: Network policies can be disabled if needed (not recommended for production)

### Configuration
Network policies are controlled by a single setting in your team configuration:
```yaml
networkPolicy:
  default_deny_all: true  # Default: enabled for security
```

## Network Policy Rules

When `default_deny_all: true`, the following network policies are automatically created:

### 1. Default Deny-All Policy
```yaml
# Blocks ALL ingress and egress traffic by default
policyTypes:
- Ingress  
- Egress
```

### 2. Same Namespace Communication
- **Purpose**: Allow pods within the same namespace to communicate
- **Scope**: Bidirectional communication (ingress + egress)
- **Use Case**: Application components talking to each other

### 3. Essential Egress Rules
Automatically allows critical outbound connections:
- **DNS Resolution**: Communication with OpenShift DNS (ports 5353 UDP/TCP)
- **Kubernetes API**: Access to cluster API server (ports 443, 6443)
- **Purpose**: Ensures pods can function properly

### 4. OpenShift Monitoring Access
- **Purpose**: Allow OpenShift monitoring to scrape metrics
- **Source**: `network.openshift.io/policy-group: monitoring` namespaces
- **Direction**: Ingress only

### 5. OpenShift Ingress Access
- **Purpose**: Allow ingress controllers to route traffic to your applications
- **Source**: `policy-group.network.openshift.io/ingress` namespaces
- **Direction**: Ingress only

### 6. Kubernetes API Server Operator
- **Purpose**: Allow API server operator to manage resources
- **Source**: `openshift-kube-apiserver-operator` namespace
- **Direction**: Ingress only

### 7. Grafana Operator Access
- **Purpose**: Allow Grafana operator to manage Grafana instances
- **Source**: `grafana-operator` namespace
- **Direction**: Ingress only

### 8. Prometheus Federation
- **Purpose**: Allow team Prometheus to federate metrics from OpenShift monitoring
- **Target**: `openshift-monitoring` namespace
- **Port**: 9091 (federation endpoint)
- **Direction**: Egress only

### 9. Team Namespace Communication
- **Purpose**: Allow communication with other namespaces belonging to the same team
- **Selector**: `soprasteria/team: <team-name>` label
- **Direction**: Bidirectional (ingress + egress)

## Security Benefits

### Traffic Isolation
- **East-West Security**: Prevents lateral movement between different teams
- **Ingress Control**: Only authorized sources can reach your applications
- **Egress Control**: Prevents data exfiltration and unauthorized outbound connections

### Default Secure
- **Zero Trust**: No traffic is allowed unless explicitly permitted
- **Operator-Friendly**: Essential OpenShift services automatically allowed
- **Application-Ready**: Same-namespace communication enabled

### Compliance Ready
- **Audit Trail**: All network policies are declaratively defined
- **Consistent Security**: Same security model across all team namespaces
- **Documentation**: Clear documentation of allowed traffic flows

## What Traffic is Blocked

### Blocked Ingress
- Traffic from other team namespaces (unless same team)
- Direct external traffic (must go through ingress controllers)
- Inter-cluster communication from non-whitelisted sources

### Blocked Egress  
- Communication to other team namespaces (unless same team)
- Direct internet access (unless through allowed endpoints)
- Communication to cluster services not explicitly allowed

### Still Allowed
- ✅ Same namespace communication
- ✅ OpenShift system services (monitoring, ingress, API)
- ✅ DNS resolution and Kubernetes API access
- ✅ Team-to-team communication (same team label)
- ✅ Prometheus federation with OpenShift monitoring

## Troubleshooting Network Issues

### Common Issues

#### Pods Can't Communicate
**Symptom**: Applications in same namespace can't reach each other
**Solution**: Check if `default_deny_all: true` - same namespace communication should be automatically allowed

#### Monitoring Not Working
**Symptom**: No metrics showing in OpenShift console
**Solution**: Verify network policies allow OpenShift monitoring access

#### External Traffic Blocked
**Symptom**: Applications can't reach external services
**Solution**: This is expected behavior - add specific egress rules if needed

### Checking Network Policies
```bash
# List all network policies in your namespace
oc get networkpolicy -n <team-namespace>

# Describe a specific policy
oc describe networkpolicy <policy-name> -n <team-namespace>
```

### Verifying Connectivity
```bash
# Test DNS resolution
oc exec <pod-name> -n <team-namespace> -- nslookup kubernetes.default

# Test API server access  
oc exec <pod-name> -n <team-namespace> -- curl -k https://kubernetes.default:443/api
```

## Disabling Network Policies

### When to Disable
- **Development/Testing**: Temporary troubleshooting
- **Legacy Applications**: Applications requiring unrestricted network access
- **Migration Period**: Gradual adoption of network security

### How to Disable
```yaml
networkPolicy:
  default_deny_all: false  # Disables all network policies
```

### ⚠️ Security Warning
Disabling network policies removes all network security controls:
- Any pod can communicate with your applications
- No protection against lateral movement
- Compliance and security audit failures possible

## Best Practices

### For Development
- **Start Secure**: Keep network policies enabled even in development
- **Test Thoroughly**: Verify application functionality with policies enabled
- **Document Dependencies**: Note any external services your applications need

### For Production  
- **Always Enable**: Network policies should always be enabled in production
- **Monitor Access**: Regularly review network policy effectiveness
- **Document Exceptions**: Any custom network policies should be documented

### For Troubleshooting
- **Check Policies First**: Network issues often relate to policy restrictions
- **Use Logging**: Enable network policy logging for debugging
- **Test Incrementally**: Temporarily disable policies for testing (non-production only)

## Custom Network Policies

If you need additional network rules beyond the defaults, you can:

1. **Contact Platform Team**: Request additional standard policies
2. **Add Custom Policies**: Deploy additional NetworkPolicy resources in your namespace
3. **Document Changes**: Ensure custom policies are documented and reviewed

### Example Custom Policy
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-external-database
  namespace: my-team-namespace
spec:
  podSelector:
    matchLabels:
      app: my-application
  policyTypes:
  - Egress
  egress:
  - to: []
    ports:
    - protocol: TCP
      port: 5432  # PostgreSQL
```

Remember: Custom policies work alongside the default policies - they don't replace them.
