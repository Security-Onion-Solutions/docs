# ElastAlert Fields

The following list includes field names as they are formatted in Elasticsearch. ElastAlert provides its own template to use for mapping into ElastAlert, so we do not currently utilize a config file to parse data from ElastAlert.

`index:*:elastalert_status`

- alert_info.type
- alert_sent
- alert_time
- endtime
- hist
- matches
- match_body.@timestamp
- match_body.num_hits
- match_body.num_matches
- rule_name
- starttime
- time_taken