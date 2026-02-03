# Security Onion Console Logs

Standard [Security Onion Console](security-onion-console.md) logs can be found at `/opt/so/log/soc/`.

## SOC Auth Logs

SOC auth is handled by Kratos and you can read more about that at <https://github.com/ory/kratos>. SOC auth logs can be found at `/opt/so/log/kratos/`. Those logs are ingested into [Elasticsearch](elasticsearch.md) and available for searching in [Dashboards](dashboards.md), [Hunt](hunt.md), and [Kibana](kibana.md). Both [Dashboards](dashboards.md) and [Hunt](hunt.md) have pre-defined queries for SOC auth logs.