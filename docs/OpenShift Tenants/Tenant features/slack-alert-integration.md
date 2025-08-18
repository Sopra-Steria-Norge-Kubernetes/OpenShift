# Slack Alert Integration

## What is Slack Alert Integration?

Slack Alert Integration in OpenShift enables real-time monitoring and alerting by sending notifications to Slack channels. This allows teams to respond quickly to incidents. 

## Recommended implementation: Use External Secrets

The preferred method for configuring Slack webhook secrets is now through the [External Secrets integration](secret-management.md). This method allows the webhook URL to be securely retrieved from Azure Key Vault using a `ClusterSecretStore`, avoiding the need to manage and encrypt secrets manually.

We recommend adopting the External Secrets method to simplify secret management and improve security.

## Configure Slack Alert Integration with SealedSecret

If you still need to use SealedSecrets, you can configure Slack alert integration by including the following in your Helm values file:

``` yaml
...
  slack_alert_integration:
    enable: False
    alert_severity: critical # Supports multiple values separated by '|': critical|warning|info
    webhook_secret:
      encrypted_webhookURL: <slack_webhook_url_sealed_secret>
... 
```

By setting these parameters, you can enable and customize the integration to send alerts to your Slack channels.

For more information about configuring the Slack alert integration, read the following guide: 

- [Slack Alert Integration with OpenShift Alerts](../../About%20Container-Platform-as-a-Service/Service%20Breakdown/Observability/slack-integration-with-openshift-alerts.md)


## In-depth description of parameters

This integration uses an encrypted Slack webhook URL stored as a secret to send notifications to a Slack channel when an alert of specified severity is triggered.

Below is a detailed description of each variable in the `slack_alert_integration` feature:

| <div style="width:260px">**Variable**</div>                            | **Description**                                                                                  | **Example**                              | **Type**                   | **Default Value**  |
|-----------------------------------------|--------------------------------------------------------------------------------------------------|------------------------------------------|----------------------------|----------|
| `enable`                                | Toggle the Slack alert integration feature. Set to `True` to enable.                             | `False`                                  | Boolean                    | false |
| `alert_severity`                        | The severity level of alerts to be sent. Multiple severities can be specified, separated by `|`. | `critical` or `critical|warning|info` | String                     | "" |
| `webhook_secret.encrypted_webhookURL`   | The encrypted Slack webhook URL to be decrypted at runtime for sending alerts.                   | *Encrypted String*                       | Kubeseal encrypted String  | "" |


