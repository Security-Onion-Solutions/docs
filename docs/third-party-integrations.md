# Third Party Integrations

In addition to [Network](network-visibility.md) and [Host](host-visibility.md), you may want to pull in data from other third party systems. You can do that via Elastic integrations which support many of the most common products and services. You can read more about Elastic integrations at <https://docs.elastic.co/integrations>.

!!! WARNING
    
    Third party integrations are provided by Elastic and are not specifically tested by the Security Onion team. Support provided by the Security Onion team for third party integrations is considered best-effort.

## Adding an Integration

New integrations can be added to existing policies to provide increased visibility and more comprehensive monitoring.

!!! TIP
    
    When adding a new integration, it is important that you add it to an appropriate policy. 
    
    If an integration pulls the data, you should add it to the Fleet Server policy. Depending on complexity and log volume, it might make sense to stand up a Fleet Node and add your integrations to it.
    
    If an integration receives data pushed to it (for example: receiving syslog), consider adding it to the Fleet Server policy. If that is not feasible, then you can add it to the Grid Nodes policy but make sure to set the firewall rules correctly so that you are not opening ports on all of your nodes.

To add an integration to an existing policy:

- From the main Fleet page, click the `Agent policies` tab.
- Select the desired agent policy.
- Click the `Add Integration` button.
- Follow the steps for adding the integration.

!!! NOTE
    
    If the integration is designed to listen on a port to receive data, it will most likely default to listening on `localhost` only. Depending on how you are sending data to the integration, you may need to change that to `0.0.0.0` so that it can receive data from other hosts.

For examples of this process, please see the [NetFlow](netflow.md) and [pfSense](pfsense.md) sections. The [pfSense](pfsense.md) section includes a link to a video which illustrates the process.

## Adding a Custom Integration

A custom integration can be added by adding an integration such as the `Custom Logs` integration. You can specify various settings relative to the data source and define additional actions to be performed.

## Managing Integration Upgrades

!!! TIP
    
    By default, integrations are not automatically kept up to date. This avoids potential log ingest downtime if there is an issue with the latest package or if the latest package requires a manual update to your integration configuration. If you would like to automatically upgrade integrations, you can change this behavior via [Administration](administration.md) -> Configuration -> elasticfleet -> config -> auto_upgrade_integrations.

To find integrations that have upgrades available:

- Navigate to [Elastic Fleet](elastic-fleet.md).
- At the top left corner, click the menu.
- Under `Management`, select `Integrations`.
- Click the `Installed Integrations` tab.
- Review any integrations listed under `Updates available`.

## Managing Third Party Integration Index Templates

Index templates for third party integrations can be managed as described in the [Elasticsearch](elasticsearch.md) section, but first `managed_integrations` must be updated by navigating to [Administration](administration.md) --> Configuration --> manager --> managed_integrations.

## Supported Integrations

The current release of Security Onion supports all standard Elastic integrations as shown at <https://docs.elastic.co/integrations>.

## More Information

!!! NOTE
    
    You can read more about Elastic integrations at <https://docs.elastic.co/integrations>.
