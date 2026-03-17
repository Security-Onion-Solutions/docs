# Directory Structure

## /opt/so/conf

Applications read their configuration from `/opt/so/conf/`. However, please keep in mind that most config files are managed with [Salt](salt.md), so if you manually modify those config files, your changes may be overwritten at the next Salt update.

## /opt/so/log

Debug logs are stored in `/opt/so/log/`.

## /opt/so/rules

[ElastAlert](elastalert.md) and [Suricata](suricata.md) rules are stored in `/opt/so/rules/`.

## /opt/so/saltstack/local

Custom [Salt](salt.md) settings can be added to `/opt/so/saltstack/local/`.

## /nsm

The vast majority of data is stored in `/nsm/`.

## /nsm/zeek

[Zeek](zeek.md) writes its protocol logs to `/nsm/zeek/`.

## /nsm/elasticsearch

[Elasticsearch](elasticsearch.md) stores its data in `/nsm/elasticsearch/`.


## /nsm/suripcap

[Suricata](suricata.md) stores full packet capture in `/nsm/suripcap/`.