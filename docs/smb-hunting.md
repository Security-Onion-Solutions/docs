# SMB Threat Hunting

This guide describes a practical workflow for investigating Server Message Block (SMB) activity in Security Onion. It focuses on identifying activity that may be consistent with remote share enumeration, administrative-share access, and file staging between Windows systems.

SMB is commonly used for legitimate administration, software deployment, backups, and file sharing. An SMB connection or access to an administrative share is not, by itself, evidence of lateral movement. Use the workflow below to establish context and correlate network and endpoint telemetry.

## Prerequisites

Before starting:

- Deploy Security Onion 3 with a sensor that can observe the relevant east-west traffic between Windows endpoints and servers.
- Make sure Zeek is enabled and its logs are arriving in Security Onion. See [Zeek](zeek.md) and [Zeek Fields](zeek-fields.md).
- Use an isolated Active Directory lab or an environment for which you are explicitly authorized to perform the test.
- For endpoint-side corroboration, collect Windows Security events from the relevant servers and confirm that they are searchable in Security Onion.

A sensor that only sees Internet-bound traffic will not provide visibility into SMB sessions that remain inside the network. Check the monitored interfaces, VLANs, TAP/SPAN configuration, and capture-loss metrics before interpreting an absence of events.

## SMB telemetry available in Security Onion

Security Onion parses Zeek logs and makes them available in Hunt, Dashboards, and Kibana. The following datasets are particularly useful:

| Dataset | Security Onion fields | What it helps investigate |
| --- | --- | --- |
| `conn` | `source.ip`, `destination.ip`, `destination.port`, `network.protocol`, `connection.state`, `client.bytes`, `server.bytes`, `log.id.uid` | Which systems communicated over SMB, when, and with what connection characteristics. |
| `smb_mapping` | `smb.path`, `smb.share_type`, `smb.service`, `source.ip`, `destination.ip`, `log.id.uid` | Which shares or named-pipe resources were mapped during a session. |
| `smb_files` | `file.action`, `file.path`, `file.name`, `file.size`, `file.previous_name`, `source.ip`, `destination.ip`, `log.id.uid` | File-related activity that Zeek observed over SMB. |

The `log.id.uid` field is Zeek's connection UID mapped by Security Onion. It is useful for correlating related Zeek records from the same connection.

For the underlying Zeek record definitions, see the [Zeek SMB log reference](https://docs.zeek.org/en/current/logs/smb.html). For Security Onion's common Zeek field mappings, see [Zeek Fields](zeek-fields.md).

!!! NOTE
    The fields shown above reflect Security Onion's Zeek ingest mappings. If a field is absent in your environment, expand an event in Hunt and inspect the fields actually received by your deployment before building a query around it.

## 1. Find SMB connections

Open Hunt and select a time range that includes the activity you want to investigate.

Start with TCP/445:

```
event.dataset: conn AND destination.port: 445
```

If your environment still uses NetBIOS-based SMB, also review TCP/139:

```
event.dataset: conn AND destination.port: 139
```

Use the Group By function, or add an OQL segment, to summarize the source and destination pairs:

```
event.dataset: conn AND destination.port: 445 | groupby source.ip destination.ip
```

Look for patterns such as:

- A workstation initiating SMB sessions to many servers in a short period.
- A source host contacting servers it does not normally administer.
- SMB connections from an unexpected workstation or subnet.
- Repeated connection attempts, unusual time windows, or unexpected data volumes.

Compare these observations with your asset inventory and known administration, backup, and software-distribution systems.

## 2. Review share mappings

Next, search the SMB share-mapping dataset:

```
event.dataset: smb_mapping
```

Useful fields include:

- `smb.path`: the share or named-pipe path, such as `\\\\SERVER\\ADMIN$`, `\\\\SERVER\\C$`, or `\\\\SERVER\\IPC$`.
- `smb.share_type`: the resource type, such as a disk share or a pipe.
- `smb.service`: the service associated with the mapped resource.
- `source.ip` and `destination.ip`: the client and server involved in the session.

Summarize the activity by source and share path:

```
event.dataset: smb_mapping | groupby source.ip smb.path
```

Administrative shares such as `ADMIN$`, `C$`, and `IPC$` are common in Windows environments and may be accessed by legitimate management tools. Treat them as investigation pivots, not automatic indicators of compromise.

Prioritize mappings that are unusual for the source host, involve unexpected servers, or occur alongside other suspicious activity.

## 3. Review file-related SMB activity

Search the SMB file dataset:

```
event.dataset: smb_files
```

Review `file.action`, `file.path`, `file.name`, `file.size`, and the source and destination addresses. Depending on the activity and the Zeek configuration, these records may help identify files opened, renamed, deleted, or otherwise observed during an SMB session.

You can group the results to identify repeated or unusual combinations:

```
event.dataset: smb_files | groupby source.ip destination.ip file.action file.name
```

!!! IMPORTANT
    Do not assume that Zeek records every file read or write. The Zeek SMB script has a configurable `SMB::logged_file_actions` set, and the stock script's default set is selective. Confirm the effective setting for the Zeek version and policy deployed in your environment before treating missing file records as evidence that no file activity occurred. See the [Zeek SMB script](https://github.com/zeek/zeek/blob/master/scripts/base/protocols/smb/main.zeek).

A lack of `smb_files` events does not prove that no file transfer took place. Use the connection metadata, share mappings, packet visibility, and endpoint logs together.

## 4. Correlate related activity

When you find an interesting connection or share mapping:

1. Record its timestamp, source IP, destination IP, and `log.id.uid`.
2. Use the SOC Correlate action where available to pivot to related records by UID or other supported identifiers.
3. Compare the activity with other SMB sessions from the same source and destination.
4. Check whether the same account or host performed related activity elsewhere in the environment.
5. Review available packet capture when the event contains the network tuple needed to pivot to PCAP.

Security Onion's [Dashboards documentation](dashboards.md) describes the Correlate action and the available search pivots. A related record is useful context, but correlation alone does not establish malicious intent.

## 5. Add Windows share-auditing events

Network telemetry is more useful when it can be corroborated with the server's own audit trail.

On Windows file servers, consider enabling the relevant Advanced Audit Policy settings for **Audit File Share** and, where the additional detail is justified, **Audit Detailed File Share**. Forward the Security event channel using your approved endpoint-log collection method and verify that events are searchable before relying on them.

Two useful event IDs are:

- **5140 — A network share object was accessed.** Microsoft documents that this event is generated once per session when the first access attempt is made.
- **5145 — A network share object was checked to see whether the client can be granted the requested access.** It can provide details such as the account, source address, share, relative target name, and requested access. It may generate substantial volume on busy file servers.

See Microsoft's documentation for [Audit File Share](https://learn.microsoft.com/windows/security/threat-protection/auditing/audit-file-share), [Event 5140](https://learn.microsoft.com/windows/security/threat-protection/auditing/event-5140), and [Event 5145](https://learn.microsoft.com/windows/security/threat-protection/auditing/event-5145).

Correlate the server-side account and share details with the source and destination observed by Zeek. The exact Windows event field names available in Hunt depend on the event collection and ingest configuration in your deployment.

## 6. Generate controlled lab activity

To validate that your sensor and searches are working, generate a small amount of authorized SMB activity from a lab workstation to a lab server.

For example, NetExec can enumerate the shares exposed by a target using a dedicated lab account:

```
nxc smb <TARGET_IP> -u <LAB_USER> -p '<LAB_PASSWORD>' --shares
```

Replace the placeholders with the address and credentials of your own isolated lab. Do not run the test against systems outside your authorization, and avoid placing real production passwords in shell history.

After the command completes:

1. Search for the workstation-to-server connection in `event.dataset: conn`.
2. Look for the corresponding entry in `event.dataset: smb_mapping`.
3. Confirm that the source, destination, timestamp, and share path match the lab activity.
4. If you collect Windows Security events, compare the server-side audit records with the Zeek timeline.

For a broader explanation of NetExec's SMB authentication and share-enumeration workflows, see the [NetExec practical guide on HackITA](https://en.hackita.it/articoli/netexec/).

## 7. Interpreting findings

Increase the priority of an investigation when multiple independent observations line up, for example:

- A workstation that does not normally administer servers maps administrative shares on several hosts.
- The activity occurs outside the host's normal maintenance window.
- The same source then accesses unusual file paths or transfers files to multiple destinations.
- Server-side audit events identify an unexpected account or source address.
- The activity is followed by other suspicious remote-administration or authentication events.

Common benign explanations include endpoint management, backup software, patch distribution, inventory scanners, and administrator activity. Validate the owner and purpose of the source system before escalating.

## Visibility limitations

The quality of an SMB hunt depends on network placement, packet completeness, protocol visibility, and endpoint logging. Asymmetric routing, packet loss, encrypted SMB sessions, or missing Windows audit events can reduce the available detail.

Use the absence of an event cautiously. A single SMB connection, an administrative-share mapping, or a file event is not enough to conclude that lateral movement occurred. Establish a baseline for normal SMB behavior and make decisions from correlated evidence.

## Further reading

- [Security Onion: Zeek](zeek.md)
- [Security Onion: Zeek Fields](zeek-fields.md)
- [Security Onion: Dashboards and OQL](dashboards.md)
- [Zeek: SMB Logs](https://docs.zeek.org/en/current/logs/smb.html)
- [Microsoft: Audit File Share](https://learn.microsoft.com/windows/security/threat-protection/auditing/audit-file-share)
