# Sigma Logsource Coverage

[Sigma](sigma.md) rules declare the telemetry they need via a `logsource` (product + category or service). Whether a rule can actually fire on your grid depends on two things: whether Security Onion **collects** that telemetry, and whether the Sigma **conversion pipeline** maps the rule's logsource and field names onto the fields Security Onion actually stores. This page maps the endpoint and cloud logsources in the SigmaHQ ruleset across both, so you can tell at a glance which rules work out of the box, which need additional endpoint configuration, and which need telemetry Security Onion does not collect.

By default Security Onion imports the SigmaHQ **core** package (stable/test rules of high/critical level) plus the **emerging threats add-on** — see [Sigma Packages](sigma.md#sigma-packages) to change this. Detection counts are shown as **default (all rules)**: rules in the default packages, with the **all-rules** package total (every stable/test/experimental rule of medium level and above) in parentheses. Counts are current as of mid-2026. Note that a rule being *supported* is separate from it being *enabled* — see [Enable Sigma Rules on Import](sigma.md#enable-sigma-rules-on-import) for controlling which rules are active by default.

**How to read the tables:**

- ✅ **Supported** — telemetry is collected once the Security Onion [Elastic Agent](elastic-agent.md) (which includes Elastic Defend) is deployed to the endpoint, and the conversion pipeline scopes the rule correctly. No extra configuration.
- ⚠️ **Partial** — a subset of the category's telemetry is collected or mapped; some rules in the category will never match. See notes.
- 🔧 **Requires setup** — telemetry exists only if you configure something extra (deploy [Sysmon](sysmon.md), enable a Windows audit feature, or add an [integration](third-party-integrations.md)).
- ❌ **Not collected** — no equivalent telemetry; these rules will never fire.

The **Playbooks** column shows Guided Analysis Playbook status for each logsource (see [Detections](detections.md)): ✅ playbooks are published for this logsource, 🔄 in development, **Planned**, or — not currently planned.

## Windows endpoint — Elastic Defend

Elastic Defend is the default endpoint telemetry agent for Security Onion. These categories are fully covered by Defend events, and the conversion pipeline scopes each one to the matching Defend event type. They work out of the box.

| Logsource | Detections | Telemetry | Playbooks | Notes |
|---|---:|---|---|---|
| windows / process_creation | 732 (1,297) | ✅ Elastic Defend (`endpoint.events.process`) | ✅ | |
| windows / file_event | 130 (199) | ✅ Elastic Defend (`endpoint.events.file`) | ✅ | |
| windows / registry_set | 113 (208) | ✅ Elastic Defend (`endpoint.events.registry`) | ✅ | Registry value modifications |
| windows / image_load | 56 (109) | ✅ Elastic Defend (`endpoint.events.library`) | ✅ | DLL hashes and code signatures are recorded on the load event |
| windows / registry_event | 30 (40) | ⚠️ Elastic Defend (partial) | ✅ | Defend records value modifications only. Rules matching key paths work; rules requiring key create/delete/rename semantics need [Sysmon](sysmon.md) EID 12/14 (7 of the 40 all-rules detections). Playbooks cover the Defend-answerable subset |
| windows / network_connection | 25 (51) | ✅ Elastic Defend (`endpoint.events.network`) | ✅ | Rules matching on `DestinationHostname` may not fire — Defend rarely records a destination domain on connections |
| windows / dns_query | 10 (24) | ✅ Elastic Defend (`endpoint.events.network` DNS lookups) | ✅ | Includes the querying process |
| windows / driver_load | 7 (9) | ✅ Elastic Defend (`endpoint.events.library`, driver events) | ✅ | Defend records driver loads natively; the pipeline scopes these to `event.category:driver` |
| windows / file_delete | 4 (12) | ✅ Elastic Defend (`endpoint.events.file`) | ✅ | |
| windows / file_rename | 0 (1) | ✅ Elastic Defend (`endpoint.events.file`) | ✅ | |

## Windows endpoint — requires Sysmon

Elastic Defend does not emit equivalent events for these categories. Deploying [Sysmon](sysmon.md) with an appropriate configuration provides the telemetry; without it, these rules will never fire.

!!! WARNING

    The conversion pipeline does not currently add Sysmon channel or event-ID scoping for these categories — converted rules rely on their field names alone to select the right events. Because some Sysmon fields are renamed to generic ECS fields (e.g. `SourceImage` → `process.executable`), a converted rule can match unrelated events. If you deploy Sysmon and enable rules in these categories, review the converted queries (Detection Source → Convert) and expect to tune.

| Logsource | Detections | Telemetry | Playbooks | Notes |
|---|---:|---|---|---|
| windows / process_access | 18 (24) | 🔧 Sysmon EID 10 | — | Cross-process handle access (e.g. LSASS dumping). Defend's API telemetry does not record cross-process handle access, so it does not satisfy these rules |
| windows / pipe_created | 11 (18) | 🔧 Sysmon EID 17/18 | — | |
| windows / create_remote_thread | 9 (12) | 🔧 Sysmon EID 8 | — | |
| windows / create_stream_hash | 6 (9) | 🔧 Sysmon EID 15 | — | |
| windows / registry_delete | 3 (10) | 🔧 Sysmon EID 12 | — | |
| windows / registry_add | 2 (3) | 🔧 Sysmon EID 12 | — | |
| windows / sysmon_status, sysmon_error | 2 (2) | 🔧 Sysmon service events | — | Only meaningful if Sysmon is deployed |
| windows / file_change | 1 (1) | 🔧 Sysmon EID 2 | — | File creation-time change (timestomping) |
| windows / process_tampering | 0 (1) | 🔧 Sysmon EID 25 | — | |
| windows / file_executable_detected | 0 (1) | 🔧 Sysmon EID 29 | — | |
| windows / file_access | 0 (6) | ❌ Not reliably collected | — | Requires ETW kernel-file auditing; neither Defend nor Sysmon provides general file-access events |

## Windows event log channels

These logsources target specific Windows event log channels. The default agent policy collects the core channels (Security, System, Application, Windows Defender, PowerShell, WMI-Activity); a few sources additionally require the corresponding Windows feature or audit policy to be enabled on the endpoint.

| Logsource | Detections | Telemetry | Playbooks | Notes |
|---|---:|---|---|---|
| windows / security | 92 (141) | ✅ Security channel | Planned | Some rules need specific audit policies enabled (e.g. object access, audit-log-cleared) |
| windows / ps_script | 57 (146) | 🔧 PowerShell script block logging | 🔄 In development | The PowerShell Operational channel is collected by default, but script block logging must be enabled on the endpoint via GPO (`Turn on PowerShell Script Block Logging`) |
| windows / system | 46 (69) | ✅ System channel | Planned | |
| windows / ps_module | 18 (28) | 🔧 PowerShell module logging | Planned | Channel collected by default; enable module logging via GPO |
| windows / application | 19 (27) | ✅ Application channel | Planned | |
| windows / windefend | 13 (15) | ✅ Windows Defender Operational channel | Planned | |
| windows / ps_classic_start, ps_classic_provider_start, powershell-classic | 4 (9) | ✅ Windows PowerShell (classic) channel | 🔄 In development | Collected by default |
| windows / wmi_event, wmi | 2 (5) | ✅ WMI-Activity Operational channel | ✅ | Collected by default. The channel records WMI provider loads and permanent event subscriptions (EID 5857–5861) natively — Sysmon EID 19–21 is not required. Rules matching on the Sysmon field names (`EventNamespace`, `Consumer`, `Filter`) will not fire against the channel's fields; match on the event ID instead |
| windows / other service channels | ~41 (~76) | 🔧 Varies | — | ~30 low-volume channels (AppLocker, BITS, CodeIntegrity, Exchange management, task scheduler, print service, NTLM, …). Conversion scoping is automatic, but the channels are not collected by the default agent policy; each needs its channel added and, in some cases, the Windows feature enabled |

## Linux and macOS endpoints

Elastic Defend covers the core endpoint categories on Linux and macOS as well.

| Logsource | Detections | Telemetry | Playbooks | Notes |
|---|---:|---|---|---|
| linux / process_creation | 51 (110) | ✅ Elastic Defend | Planned | |
| linux / auditd | 12 (28) | 🔧 auditd + integration | — | Requires auditd on the endpoint and the Auditd integration; rule field names (`type`, `a0`, …) additionally need custom field mapping |
| linux / file_event | 10 (14) | ✅ Elastic Defend | Planned | |
| linux / network_connection | 5 (5) | ✅ Elastic Defend | Planned | |
| linux / other | ~20 (~26) | 🔧 Syslog / integrations | — | sshd, sudo, cron, clamav, vsftpd, guacamole, generic syslog. Only `linux/auth` has a conversion mapping; the rest need custom field mappings |
| macos / process_creation | 10 (49) | ✅ Elastic Defend | Planned | |
| macos / file_event | 2 (3) | ✅ Elastic Defend | Planned | |

## Cloud and SaaS

These require the corresponding Elastic [integration](third-party-integrations.md) to be configured — nothing is collected out of the box. Conversion support varies: **m365 rules are fully mapped** to the O365 integration's fields and dataset; the other products currently have no conversion mappings, so even with the integration collecting data, their rules need custom field mappings before they can match.

| Logsource | Detections | Telemetry | Playbooks | Conversion mapping |
|---|---:|---|---|---|
| azure (activitylogs, auditlogs, signinlogs, riskdetection, pim) | 51 (123) | 🔧 Azure integrations | — | Custom field mapping required |
| aws / cloudtrail | 12 (42) | 🔧 AWS CloudTrail integration | — | Custom field mapping required |
| okta | 6 (20) | 🔧 Okta integration | — | Custom field mapping required |
| bitbucket / audit | 4 (12) | 🔧 Bitbucket audit logs | — | Custom field mapping required |
| github / audit | 4 (9) | 🔧 GitHub audit log integration | — | Custom field mapping required |
| m365 (threat_management, audit, exchange) | 1 (18) | 🔧 Microsoft 365 integrations | — | ✅ Mapped (`o365.audit`) |
| gcp (gcp.audit, google_workspace) | 0 (25) | 🔧 GCP / Google Workspace integrations | — | Custom field mapping required |
| kubernetes / audit | 0 (9) | 🔧 Kubernetes audit integration | — | Custom field mapping required |

