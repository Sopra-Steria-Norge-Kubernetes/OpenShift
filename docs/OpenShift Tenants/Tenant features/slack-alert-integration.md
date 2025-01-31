# Slack Alert Integration

## What is Slack Alert Integration?

Slack Alert Integration in OpenShift enables real-time monitoring and alerting by sending notifications to Slack channels. This integration allows teams to receive alerts based on the severity of issues occurring within the Openshift cluste. 

By configuring Slack alert integration, you ensure that critical events are communicated immediately to the relevant teams, improving incident response times and collaboration.

## How to configure Slack Alert Integration

To configure Slack alert integration for your OpenShift tenant using our Helm chart, include the following YAML configuration in your Helm values file:

``` yaml
...
  slack_alert_integration:
    enable: False
    alert_severity: critical # If multiple; critical|warning|info
    webhook_secret:
      encrypted_webhookURL: <webhook to slack channel encrypted with sealedsecrets>
... 
```

By setting these parameters, you can enable and customize the integration to send alerts to your Slack channels, specifying the severity levels and providing the encrypted webhook URL. This setup ensures that important alerts are securely and efficiently communicated to your team.

For more information about configuring the Slack alert integration, read the following guide: 

- [Slack Alert Integration with OpenShift Alerts](../../Monitoring/slack-integration-with-openshift-alerts.md)


## In-depth description of parameters

This integration uses an encrypted Slack webhook URL stored as a secret to send notifications to a Slack channel when an alert of specified severity is triggered.

Below is a detailed description of each variable in the `slack_alert_integration` feature:

| <div style="width:260px">**Variable**</div>                            | **Description**                                                                                  | **Example**                              | **Type**                   | **Default Value**  |
|-----------------------------------------|--------------------------------------------------------------------------------------------------|------------------------------------------|----------------------------|----------|
| `enable`                                | Toggle the Slack alert integration feature. Set to `True` to enable.                             | `False`                                  | Boolean                    | false |
| `alert_severity`                        | The severity level of alerts to be sent. Multiple severities can be specified, separated by `|`. | `critical` or `critical|warning|info` | String                     | "" |
| `webhook_secret.encrypted_webhookURL`   | The encrypted Slack webhook URL to be decrypted at runtime for sending alerts.                   | *Encrypted String*                       | Kubeseal encrypted String  | "" |


