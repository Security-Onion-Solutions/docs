# Release Notes

### Known Issues

[Auto State Apply](salt.md#auto-state-apply) detects configuration changes saved in [Administration](administration.md) --> Configuration and rule updates on the manager node. Files that you create or edit directly under `/opt/so/saltstack/local/salt/` are not detected. After adding or changing any of the following, apply the relevant state from the manager or wait for the next scheduled highstate (see [Highstate Interval](salt.md#highstate-interval)):

- [Zeek](zeek.md) intel in `/opt/so/saltstack/local/salt/zeek/policy/intel/`
- [Zeek](zeek.md) custom packages in `/opt/so/saltstack/local/salt/zeek/zkg/`
- [Elasticsearch](elasticsearch.md) custom ingest parsers in `/opt/so/saltstack/local/salt/elasticsearch/files/ingest/`
- [RBAC](rbac.md) custom Elastic stack role files in `/opt/so/saltstack/local/salt/elasticsearch/roles/`
- [Logstash](logstash.md) custom pipeline configuration files in `/opt/so/saltstack/local/salt/logstash/pipelines/config/custom/`

For all other known issues, please see <https://github.com/Security-Onion-Solutions/securityonion/issues>.

### Release History

3.2.0 [20260729] Changes
----------------------

- FEATURE: Initial implementation of agentic framework
- FEATURE: Autodetect salt apply state from SOC config audit history
- FEATURE: Config audit history w/ restore
- FEATURE: Datastream Lifecycle Management
- FEATURE: Guided Analysis Progressive Loading <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/16091">#16091</a>
- FEATURE: Limit certain config settings to specific node types <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15972">#15972</a>
- FEATURE: Map Antivirus Sigma rules to Elastic Defend <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/14468">#14468</a>
- FEATURE: Sigma Playbooks - Initial Set <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/16090">#16090</a>
- FEATURE: Support ES|QL in Sigma detections
- FEATURE: Support Suricata Transactional rule dir <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15948">#15948</a>
- FEATURE: Updated default Hunt query <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/16026">#16026</a>
- FIX: Allow manager to run two full highstates during soup <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15986">#15986</a>
- FIX: Allow periods in NIC names <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/16060">#16060</a>
- FIX: Disable Zeek icsnpp-modbus script <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/16110">#16110</a>
- FIX: Do not allow login redirects to API URLs <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/16065">#16065</a>
- FIX: Elastic Defend incompatible with linux 7+ kernels
- FIX: Elastic Fleet server state persistence <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/16051">#16051</a>
- FIX: Elasticfleet: server urls auto updating when opted out <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15960">#15960</a>
- FIX: Elasticsearch GC log rotate <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/16034">#16034</a>
- FIX: Elasticsearch: index template partial duplicate <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15959">#15959</a>
- FIX: Ensure so-yaml.py updated during soup <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/16066">#16066</a>
- FIX: Import/Eval error in elasticsearch configuration script <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/16025">#16025</a>
- FIX: Improve elastic agent install outcome to check that the installation is healthy
- FIX: Improve Elasticsearch scripts runtime <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15987">#15987</a>
- FIX: Improve Group Metrics Layout on Alert Page
- FIX: Improve Hunt Query Box UI on Smaller Screens
- FIX: Improve logging when Alerts fail to Ack <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15999">#15999</a>
- FIX: Missing esheap pillar value breaks highstate on Elasticsearch nodes <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/16108">#16108</a>
- FIX: Nav Bar Hover Misalignment
- FIX: Refreshing browser while in hunt drops index filters <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15331">#15331</a>
- FIX: Rename Connect API to Security Onion API <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15921">#15921</a>
- Fix: Rework soup postupgrade_changes <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15946">#15946</a>
- FIX: Run Elastic Agent regenerate installers script
- FIX: Salt: server restart and highstate issues
- FIX: so-start | so-stop | so-restart utilities <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/16014">#16014</a>
- FIX: Soup should run so-config-backup script <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15901">#15901</a>
- FIX: Soup verify an upgrade is available prior to running elasticsearch upgrade compatibility check
- FIX: Suricata rule reload should not report failure if a reload is already in progress <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/16016">#16016</a>
- UPGRADE: alpine base images to 3.24.1 <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15992">#15992</a>
- UPGRADE: Axios to 1.18.1 <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15950">#15950</a>
- UPGRADE: CyberChef to 11.2.0 <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15997">#15997</a>
- UPGRADE: Dompurify to 3.4.12 <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15977">#15977</a>
- UPGRADE: Elasticsearch 9.3.7 <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/16063">#16063</a>
- UPGRADE: golang to 1.26.4 <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15988">#15988</a>
- UPGRADE: InfluxDB to 2.9.1 (UI to 2.9.0) <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15993">#15993</a>
- UPGRADE: js-yaml to 4.3.0 <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15976">#15976</a>
- UPGRADE: Kafka to 4.3.1 <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/16001">#16001</a>
- UPGRADE: Kratos and Hydra google/x/net Go deps <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/16048">#16048</a>
- UPGRADE: Migration of more images to UBI 9.7 <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/16010">#16010</a>
- UPGRADE: nginx to 1.31.2 <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15996">#15996</a>
- UPGRADE: node to 26.3.1 <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15990">#15990</a>
- UPGRADE: OpenCanary to 0.9.8 <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/16003">#16003</a>
- UPGRADE: Oracle UEK8 Kernel
- UPGRADE: Postgres to 17.10 <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15998">#15998</a>
- UPGRADE: pySigma & sigma-cli <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15616">#15616</a>
- UPGRADE: Redis to 7.4.9 <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15994">#15994</a>
- UPGRADE: registry to 3.1.1 <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15991">#15991</a>
- UPGRADE: SOC Go dependencies <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/16032">#16032</a>
- UPGRADE: Suricata to 8.0.6 <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/16042">#16042</a>
- UPGRADE: Telegraf to 1.39.0 <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15995">#15995</a>
- UPGRADE: Zeek to 8.0.9 <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/16040">#16040</a>

3.1.0 Hotfix [20260528] Changes
-------------------------------

- FIX: Grids with multiple heavy nodes fail Elasticsearch upgrade verification for 3.1.0
- FIX: Grids using custom logstash pipeline(s) may have stale pillar entries <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15932">#15932</a>

3.1.0 [20260521] Changes
------------------------

- FEATURE: Add Postgres support for future features
- FEATURE: Add bonded NIC support for management interfaces <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15548">#15548</a>
- FEATURE: Add ingest latency metric
- FEATURE: Allow the setup of bond1 for management for ISO installs <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15865">#15865</a>
- FEATURE: Elastic Fleet continuously validate output policy
- FEATURE: RAID monitoring for hypervisor VMs <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15809">#15809</a>
- FEATURE: Restore Suricata Overrides from backup <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15881">#15881</a>
- FEATURE: Sigma mappings - M365 & Fortigate <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15882">#15882</a>
- FEATURE: Simplified Onion AI setup for regions outside US <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15773">#15773</a>
- FEATURE: Support Azure OpenAI endpoints <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15841">#15841</a>
- FIX: 'Investigate' using inaccessible local model shows "insufficient credits"
- FIX: Add options selection to annotations <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15744">#15744</a>
- FIX: Appliance images in SOC grid misaligned <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15713">#15713</a>
- FIX: Consider setting Elastic Agent output level to warning only <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15431">#15431</a>
- FIX: Deterministically sort threshold.conf <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15815">#15815</a>
- FIX: Improve elastic agent install outcome to check that the installation is healthy
- FIX: Improve lucene and elastic query param validation <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15860">#15860</a>
- FIX: Improve reverse DNS lookups success rate <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15760">#15760</a>
- FIX: Improve usability for visually impaired users
- FIX: JA4+ license hyperlink <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15717">#15717</a>
- FIX: Make SOC and Kratos enabled annoations readonly <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15827">#15827</a>
- FIX: Modifying detection templates in config causes SOC to crash loop <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15798">#15798</a>
- FIX: Need better user feedback when attaching assistant chat to a case <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15689">#15689</a>
- FIX: Node descriptions containing both spaces and numbers prevent pillar creation <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15540">#15540</a>
- FIX: Prevent excessive OnionAI query length
- FIX: Reactor sominion_setup <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15834">#15834</a>
- FIX: Refactor Detections backup <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/14992">#14992</a>
- FIX: Reinstall <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15811">#15811</a>
- FIX: SOUP verify all Elasticsearch nodes are compatible with the next Elasticsearch version <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15908">#15908</a>
- FIX: Suricata pcap-log max-files rounds to 0 when calculated value is between 0 and 1 <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15740">#15740</a>
- FIX: UI should show the name of the current Dashboard <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15703">#15703</a>
- FIX: Use hunt action link for case observable hunt pivots <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15752">#15752</a>
- FIX: Use safeload for loading filecheck config <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15859">#15859</a>
- FIX: Zeek ingest pipeline for JA4d.log <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15886">#15886</a>
- UPGRADE: Axios to 1.15.0 in SOC <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15774">#15774</a>
- UPGRADE: CyberChef to 11.0.0 <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15890">#15890</a>
- UPGRADE: Elasticsearch to 9.3.3
- UPGRADE: Kratos and Hydra 26.2.0+pgx <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15796">#15796</a>
- UPGRADE: SOC Go dependencies <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15795">#15795</a>
- UPGRADE: SOC frontend dependency libs <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15848">#15848</a>
- UPGRADE: Suricata to 8.0.5 <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15903">#15903</a>
- UPGRADE: Zeek to 8.0.8 <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15794">#15794</a>
- UPGRADE: nginx to 1.30.1 <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15891">#15891</a>

3.0.0 [20260331] Changes
----------------------

- FEATURE: Configurable Elasticsearch vm.max_map_count setting
- FEATURE: Dynamically load Zeek plugins on zeek startup <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15546">#15546</a>
- FEATURE: Enable JA4+ License Acceptance <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15560">#15560</a>
- FEATURE: Parsing for Zeek websockets logs <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15657">#15657</a>
- FEATURE: Refresh login page with updated look
- FEATURE: Refresh SOC UI with updated look
- FEATURE: Support additional alt names in web cert
- FEATURE: Support docker ulimit customization <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15581">#15581</a>
- FEATURE: Suricata PCAP replacing Stenographer
- FIX: API 401 errors will no longer redirect <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15611">#15611</a>
- FIX: Cleanup file.absent and cron.absent
- FIX: Detections - Intermittent "error closing scroll" <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/14216">#14216</a>
- FIX: Duplicated user roles when refreshing frontend at Administration > Users <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15688">#15688</a>
- FIX: Enabled / Disabled Buttons for SOC Grid Configuration Options <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15649">#15649</a>
- FIX: Fix rule validators in SOC <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15533">#15533</a>
- FIX: Global override configs should not apply to certain indices <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15601">#15601</a>
- FIX: Network Transport for suricata alerts should be lowercase <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15668">#15668</a>
- FIX: Sensors are not checking in while processing long jobs <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15650">#15650</a>
- FIX: so-suricata-testrule script <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15396">#15396</a>
- FIX: STIG V1R3
- FIX: Suricata address-groups vars allow negation <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15664">#15664</a>
- FIX: Unable to create detections via Security Onion API <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15673">#15673</a>
- UPGRADE: All frontend 3rd party deps
- UPGRADE: ATTACK Navigator to 5.3.0 <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15680">#15680</a>
- UPGRADE: CyberChef to 10.22.1 <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15681">#15681</a>
- UPGRADE: ElastAlert2 to 2.28.0 <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15685">#15685</a>
- UPGRADE: Golang 3rd party deps <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15647">#15647</a>
- UPGRADE: Golang to 1.26.1 <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15580">#15580</a>
- UPGRADE: Hydra to 25.4.0 <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15678">#15678</a>
- UPGRADE: Kafka to 3.9.2 <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15684">#15684</a>
- UPGRADE: Kratos to 25.4.0 <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15677">#15677</a>
- UPGRADE: Nginx to 1.29.6 <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15686">#15686</a>
- UPGRADE: OpenCanary to 0.9.7 <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15679">#15679</a>
- UPGRADE: Redis to 7.2.13 <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15682">#15682</a>
- UPGRADE: Suricata to 8.0.4 <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15625">#15625</a>
- UPGRADE: Telegraf to 1.38.0 <a href="https://github.com/Security-Onion-Solutions/securityonion/issues/15683">#15683</a>
- UPGRADE: Update Docker base images
