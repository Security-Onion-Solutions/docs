# FAQ

## Table of Contents

- [Install / Update / Upgrade](#install-update-upgrade)
- [Users / Passwords](#users-passwords)
- [Support / Help](#support-help)
- [IDS engines](#ids-engines)
- [Security Onion internals](#security-onion-internals)
- [Tuning](#tuning)
- [Common Problems](#common-problems)
- [Miscellaneous](#miscellaneous) 

## Install / Update / Upgrade

### Why won't the ISO image boot on my machine?

Please see the [Help](help.md) section.

### What's the recommended procedure for installing Security Onion?

Please see the [Installation](installation.md) section.

### What's the recommended procedure for configuring Security Onion?

Please see the [Configuration](configuration.md) section.

### What if I receive a `The IP being routed by Linux is not the IP address assigned to the management interface` error message?

Please see the warning about this in the [Configuration](configuration.md) section.

### What languages are supported?

We only support the English language at this time.

### How do I install Security Onion updates?

Please see the [soup](soup.md) section.

### What connectivity does Security Onion need to stay up to date?

Please see the [Firewall](firewall.md) section.

### What do I need to do if I'm behind a proxy?

Please see the [Proxy](proxy.md) section.

### Can I run Security Onion on Raspberry Pi or some other non-x86 box?

No, we only support x86-64 (standard Intel/AMD 64-bit architectures). Please see the [Hardware](hardware.md) section.

[Back to Top](#faq)

## Users / Passwords

### What is the password?

Please see the [Passwords](passwords.md) section.

### How do I add a new user account?

Please see the [Adding Accounts](adding-accounts.md) section.

[Back to Top](#faq)

## Support / Help

### Where do I send questions/problems/suggestions?

Please see the [Community Support](community-support.md) section.

### Is commercial support available for Security Onion?

Yes, we offer commercial support at <https://securityonionsolutions.com>.

[Back to Top](#faq)

## IDS engines

### Can Security Onion run in `IPS` mode?

No, Security Onion does not support blocking traffic. Most organizations have some sort of Next Generation Firewall (NGFW) with IPS features and that is the proper place for blocking to occur. Security Onion is designed to monitor the traffic that makes it through your firewall.

[Back to Top](#faq)

## Security Onion internals

### Where can I read more about the tools contained within Security Onion?

Please see the [Tools](tools.md) section.

### What's the directory structure of `/nsm`?

Please see the [Directory](directory.md) section.

### Why does Security Onion use `UTC`?

Please see the [Time Zones](time-zones.md) section.

### Why are the `timestamps` in Kibana not in UTC?

Please see the [Time Zones](time-zones.md) section.

### Why is my disk filling up?

In general, Security Onion attempts to make use of as much disk space as you give it. Depending on installation type, it should continue writing data to disk until disk usage reaches 80-90% at which point it should begin purging old data. Most disk space is used by [Elasticsearch](elasticsearch.md) or [Full Packet Capture](full-packet-capture.md) written to disk via [Suricata](suricata.md).

### How is my data kept secure?

Standard network connections to or from Security Onion are encrypted. This includes SSH, HTTPS, [Elasticsearch](elasticsearch.md) network queries, and [Salt](salt.md) minion traffic. All endpoint agent (Elastic Agent) traffic is encrypted except for binary updates, which are served from the Manager over http - these update files are cryptographically signed by Elastic and are verified before they are used. There is also the option to pull these updates via https directly from Elastic. SOC user account passwords are hashed via bcrypt in Kratos and you can read more about that at <https://github.com/ory/kratos>.

[Back to Top](#faq)

## Tuning

### How do I configure email for alerting and reporting?

Please see the [Email](email.md) section.

### How do I configure a `BPF`?

Please see the [BPF](bpf.md) section.

### How do I filter traffic?

Please see the [BPF](bpf.md) section.

### How do I exclude traffic?

Please see the [BPF](bpf.md) section.

### What are the default firewall settings and how do I change them?

Please see the [Firewall](firewall.md) section.

### What do I need to modify in order to have the log files stored on a different mount point?

Please see the [New Disk](new-disk.md) section.

[Back to Top](#faq)

## Common Problems

### Why do containers go missing?

Docker containers that stop running, due to exiting from errors or other reasons, will be automatically removed by the scheduled cleanup process.

Most container logs are redirected to their application log directory, located in `/opt/so/log`. In some cases the logs may not get written to disk, and instead must be viewed via `docker logs <container-name>` before the container is cleaned up.

### Why does ElastAlert often go missing on my Grid?

ElastAlert 2 will exit upon encountering syntax errors with rules or when Elasticsearch is not in a healthy state.

### Why does Elasticsearch go to the unhealthy state?

Elasticsearch will become unhealthy for a variety of reasons, but the most common reasons are running out of disk space and having indices with unallocated shards.

[Back to Top](#faq)

## Miscellaneous

### Where can I find interesting pcaps to replay?

Please see the [PCAPs](pcaps.md) section.

### Why is Security Onion connecting to an IP address on the Internet over port 123?

Please see the [NTP](ntp.md) section.

### Should I backup my Security Onion box?

Security Onion automatically backs up some important configuration as described in the [Backup](backup.md) section. However, there is no automated data backup. Network Security Monitoring as a whole is considered "best effort". It is not a "mission critical" resource like a file server or web server. Since we're dealing with "big data" (potentially terabytes of full packet capture) of a transient nature, backing up the data would be prohibitively expensive. Most organizations don't do any data backups and instead just rebuild boxes when necessary.

### What happened to Filebeat?

Filebeat has been replaced by [Elastic Agent](elastic-agent.md).

### What happened to Grafana?

Grafana has been replaced by [Grid](grid.md).

### What happened to Playbook?

Playbook has been replaced by [Detections](detections.md).

### What happened to Wazuh?

Wazuh has been replaced by [Elastic Agent](elastic-agent.md).

### What happened to Stenographer?

Stenographer has been replaced by [Suricata](suricata.md) [full packet capture](full-packet-capture.md).

### How can I add local rules?

Please see the [Detections](detections.md) section.

### Can I connect Security Onion to Active Directory or another OIDC provider?

Please see the [OIDC](oidc.md) section.

[Back to Top](#faq)
