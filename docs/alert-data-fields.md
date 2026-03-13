# Alert Data Fields

[Elasticsearch](elasticsearch.md) receives [NIDS](nids.md) alerts from [Suricata](suricata.md) via [Elastic Agent](elastic-agent.md) or [Logstash](logstash.md) and parses them using:

- `/opt/so/conf/elasticsearch/ingest/suricata.alert`
- `/opt/so/conf/elasticsearch/ingest/common.NIDS`
- `/opt/so/conf/elasticsearch/ingest/common`

You can find these online at:

- <https://github.com/Security-Onion-Solutions/securityonion/blob/3/main/salt/elasticsearch/files/ingest/suricata.alert>
- <https://github.com/Security-Onion-Solutions/securityonion/blob/3/main/salt/elasticsearch/files/ingest/common.nids>
- <https://github.com/Security-Onion-Solutions/securityonion/blob/3/main/salt/elasticsearch/files/ingest-dynamic/common>

You can find parsed [NIDS](nids.md) alerts in [Alerts](alerts.md), [Dashboards](dashboards.md), [Hunt](hunt.md), and [Kibana](kibana.md) via their predefined queries and dashboards or by manually searching for:

- `event.module:"Suricata"`
- `event.dataset:"alert"`

Those alerts should have the following fields:

- `source.ip`
- `source.port`
- `destination.ip`
- `destination.port`
- `network.transport`
- `rule.gid`
- `rule.name`
- `rule.rule`
- `rule.rev`
- `rule.severity`
- `rule.uuid`
- `rule.version`
