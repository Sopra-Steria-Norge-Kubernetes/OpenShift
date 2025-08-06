
# Observability Documentation

## Introduction

This documentation provides comprehensive guidance for developers on using observability features within the OpenShift Container Platform managed by Sopra Steria. Observability is crucial for maintaining healthy applications and understanding system behavior in production environments.

## What is Observability?

Observability encompasses the ability to measure, monitor, and understand the internal state of your applications and infrastructure through external outputs. In the context of OpenShift, this includes:

- **Metrics**: Quantitative data about your applications and infrastructure
- **Logs**: Detailed records of events and operations
- **Traces**: Request flow through distributed systems
- **Alerts**: Automated notifications when issues occur

## Available Observability Features

### 1. Built-in OpenShift Observability
OpenShift provides native observability capabilities through the developer console, including:
- Real-time performance dashboards
- Application metrics and monitoring
- Log aggregation and viewing
- Alert management

### 2. Team Monitoring Stack
Deploy a dedicated Prometheus-based monitoring stack for your team:
- Custom metrics collection
- Configurable retention periods
- High availability options
- Alertmanager integration
- **Note**: When routes are exposed, the metrics from Prometheus are available to anyone with network access to the OpenShift environment.

### 3. Team Grafana Instances
Access powerful visualization capabilities:
- Custom dashboards
- Multi-tenant data sources
- Role-based access control (configurable)
- Integration with team monitoring stacks


### 4. Network Security
Team namespaces are secured using network policies:
- Default deny-all network policies (enabled by default)
- Automatic allow rules for essential OpenShift services
- Intra-namespace communication allowed
- Team-to-team communication configurable
- Prometheus federation with OpenShift monitoring enabled

### 5. Network Observability
Monitor and analyze network traffic:
- Flow-based network monitoring
- Traffic topology visualization
- Network performance metrics
- Security and compliance insights

### 6. Application Monitoring
Configure monitoring for your specific applications:
- Custom Prometheus alerts
- Service level indicators (SLIs)
- Performance tracking
- Health checks and uptime monitoring

## Getting Started

### Prerequisites
- An OpenShift tenant created by Sopra Steria
- Appropriate RBAC permissions for your team
- Basic understanding of Kubernetes/OpenShift concepts

### Quick Start Guide
1. **Access Built-in Observability**: Start with the OpenShift console's observe section
2. **Configure Team Features**: Set up team monitoring stack and Grafana if needed
3. **Create Custom Dashboards**: Build visualizations specific to your applications
4. **Set Up Alerts**: Configure notifications for important events
5. **Monitor Network Traffic**: Use network observability for traffic analysis

## Documentation Structure

This documentation is organized into the following sections:

### Core Observability Features
- **[Using OpenShift Observability](./using-openshift-observability.md)**: Native OpenShift monitoring capabilities
- **[Configure Grafana and Thanos Query](./Grafana/configure-grafana-and-thanos-query.md)**: Setting up advanced visualization and querying

### Network Security
- **[Team Network Policies](team-network-policies.md)**: Understanding network security in team namespaces

### Application Monitoring
- **[Configuring Monitoring for a Tenant](./Monitoring%20and%20Logging/configuring-monitoring-for-a-tenant.md)**: Custom alerts and monitoring rules for your applications

### Network Monitoring
- **[Using Network Observability](using-network-observability.md)**: Network traffic analysis and monitoring

## Best Practices

### For Development Teams
- Start with built-in OpenShift observability before implementing custom solutions
- Use team monitoring stacks for production environments
- Configure appropriate retention periods based on your needs
- **Grafana Security**: Configure RBAC and authentication for Grafana instances
- **Prometheus Security**: Prometheus/Thanos routes are publicly accessible when exposed

### For Production Environments
- Enable high availability for critical monitoring components
- Set up comprehensive alerting for system and application health
- Regular backup and disaster recovery for observability data
- Monitor resource usage of observability components
- **Configure Grafana Authentication**: Set up proper RBAC for Grafana instances
- **Secure Prometheus Access**: Prometheus/Thanos routes lack built-in authentication

### Security Considerations
- **Network Security**: Team namespaces use default deny-all network policies
- **Grafana RBAC**: Configure authentication and role-based access for Grafana
- **Prometheus/Thanos Routes**: Monitoring stack routes are publicly accessible when enabled
- **No Prometheus Authentication**: Prometheus and Thanos lack built-in authentication
- Secure sensitive metrics and logs through network policies
- Regular security updates for observability components
- Monitor access to exposed observability endpoints

## Support and Troubleshooting

If you encounter issues with observability features:

1. **Check Prerequisites**: Ensure your tenant has the necessary permissions and configurations
2. **Review Documentation**: Each feature has detailed setup and troubleshooting guides
3. **Contact Support**: Reach out to Sopra Steria for tenant-specific issues
4. **Community Resources**: Leverage OpenShift and Prometheus community documentation

## Next Steps

Begin your observability journey by:
1. Exploring the [built-in OpenShift observability features](./using-openshift-observability.md)
2. Setting up [team monitoring capabilities](../../../OpenShift%20Teams/Team%20features/observability/observability.md)
3. Configuring [application-specific monitoring](./Monitoring%20and%20Logging/configuring-monitoring-for-a-tenant.md)

Remember that effective observability is an iterative process. Start with basic monitoring and gradually add more sophisticated features as your applications and understanding grow.