# Host Visibility Overview

More and more of our network traffic is encrypted these days and that's a good thing for privacy but it's somewhat of a blind spot for us as defenders. Host visibility can help fill in those blind spots. You can send host logs to Security Onion via your choice of either [Elastic Agent](elastic-agent.md) or [Syslog](syslog.md):

- Choose [Elastic Agent](elastic-agent.md) for comprehensive telemetry if you can install an agent on the host.
- Choose [Syslog](syslog.md) if you can't install an agent but the device supports sending standard syslog. Examples include firewalls, switches, routers, and other network devices.

For Windows endpoints, you can optionally augment the standard Windows logging with [Sysmon](sysmon.md).
