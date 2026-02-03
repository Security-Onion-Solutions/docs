# pfSense

pfSense is a free and open firewall that can be found at <https://www.pfsense.org/>. There are a few different ways in which you can integrate pfSense into your Security Onion deployment.

The simplest method of integrating pfSense into your Security Onion deployment is to configure pfSense to send its firewall logs to Security Onion. Security Onion has a couple of options for ingesting logs from pfSense firewalls: a simple parser and the more comprehensive Elastic Integration for pfSense. We recommend using the more comprehensive option by following the steps in the Elastic Integration section below. You can also follow along with our video at <https://www.youtube.com/watch?v=aoH8qZwAxek>.

You can also configure pfSense to send [NetFlow](netflow.md) data to your Security Onion deployment. This may provide some additional visibility if you can't deploy a full Security Onion network sensor to monitor that network segment.

## Simple Parser

The first option for collecting pfSense firewall logs is to use our simple parser. Please note that this only supports `filterlog` (actual firewall logs) and no other logs.

!!! WARNING
    
    This simple parser will be phased out over time in favor of the more comprehensive Elastic Integration for pfSense below.

To use the simple parser, first go to [Administration](administration.md) --> Configuration --> firewall --> hostgroups. 

![Image](images/config-item-firewall.png)

Once there, select the `syslog` option, specify the IP address of the pfSense firewall, and click the checkmark to save. Then click the `SYNCHRONIZE Grid` button under the `Options` menu at the top of the page.
   
Next, configure your pfSense firewall to send `syslog` to the IP address of your Security Onion box. If you are using pfSense 2.6.0 or higher, make sure that `Log Message Format` is set to `BSD (RFC 3164, default)`. 

Once all configuration is complete, you should be able to go to [Dashboards](dashboards.md) and select the Firewall dashboard to see your firewall logs.

## Elastic Integration for pfSense

The second option for collecting pfSense firewall logs is using the Elastic Integration for pfSense (<https://docs.elastic.co/integrations/pfsense>). This integration is more comprehensive than the simple parser above and supports more log types.

First, add the pfSense integration and configure the pfSense firewall:

1. Go to [Elastic Fleet](elastic-fleet.md), click the `Agent policies` tab, and then click the desired policy (for example `so-Grid-nodes_general`).
2. Click the `Add integration` button.
3. Search for `pfSense` and then click on the `pfSense` integration.
4. The Elastic Integration page will show instructions for configuring pfSense. Follow these instructions but please note that the Elastic Integration expects to receive pfSense logs on port 9001 by default.
5. Once you've configured pfSense, then go back to the Elastic screen and click the `Add pfSense` button.
6. On the `Edit pfSense integration` screen, go to the `Syslog Host` field and change `localhost` to `0.0.0.0`.
7. Click the `Save and continue` button and then click `Save and deploy changes`.

Next, allow the traffic from the pfSense firewall to port 9001. These instructions assume that this is the first firewall change you have made and therefore refer to `customhostgroup0` and `customportgroup0`. If those have already been used, select the next available hostgroup and portgroup.

1. Navigate to [Administration](administration.md) --> Configuration.
2. At the top of the page, click the `Options` menu and then enable the `Show advanced settings` option.
3. On the left side, go to `firewall`, select `hostgroups`, and click the `customhostgroup0` group. On the right side, enter the IP address of the pfSense firewall and click the checkmark to save.
4. On the left side, go to `firewall`, select `portgroups`, select the `customportgroup0` group, and then click `udp`. On the right side, enter `9001` and click the checkmark to save.
5. On the left side, go to `firewall`, select `role`, and then select the node type that will receive the pfSense logs. Then drill into `chain` --> `INPUT` --> `hostgroups` --> `customhostgroup0` --> `portgroups`. On the right side, enter `customportgroup0` and click the checkmark to save.
6. If you would like to apply the rules immediately, click the `SYNCHRONIZE Grid` button under the `Options` menu at the top of the page.

Once all configuration is complete, you should be able to go to [Dashboards](dashboards.md) and select the `Firewall - pfSense` dashboard to see your firewall logs.