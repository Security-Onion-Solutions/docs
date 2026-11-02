# Notifications

!!! NOTE

    This is an enterprise-level feature of Security Onion. Contact Security Onion Solutions, LLC via our website at <https://securityonion.com/pro> for more information about purchasing a Security Onion Pro license to enable this feature.

Security Onion Pro includes a centralized, multi-channel notification engine built natively into the Security Onion Console (SOC) backend. This system provides a unified framework for dispatching notifications across multiple communication platforms, tracking in-app alerts, and controlling alert delivery through granular activation schedules and severity filters.

## Overview: Notifications vs ElastAlert 2 Notifications

Security Onion provides two notification mechanisms that serve different roles across the platform:

| Capability | SOC Notifications (Native) | ElastAlert 2 Notifications |
|---|---|---|
| **Architecture** | Native Go service built directly into `securityonion-soc` | Python service running the ElastAlert 2 daemon |
| **Primary Scope** | Grid infrastructure events, asynchronous operational jobs, and centralized alerting | [Sigma](sigma.md) detection rules querying Elasticsearch |
| **Supported Triggers** | • **[Grid Alarms & Metrics](grid.md#alarms)**: Host status, CPU/memory threshold breaches, disk space watermarks<br>• **PCAP Jobs**: Notification when packet capture extractions complete<br>• **Reports**: Automated and on-demand PDF/CSV report delivery<br>• **Future Release**: Detection alert notifications (Suricata, Strelka, and Sigma/Elastic detections will route directly through this module, eliminating ElastAlert 2)<br>• **Onion AI**: AI assistant findings and suggested actions<br>• **Manual & Ad-Hoc**: Operator-initiated test and broadcast notifications | • [Sigma](sigma.md) detections tagged with `so.notification`<br>• Custom ElastAlert 2 rule files |
| **In-App Delivery** | Native SOC top-bar Notification Bell panel with per-user read/unread and dismissal tracking | Not available (external alerters only) |
| **Destinations** | Centralized destination channels: In-App SOC Bell, Email (SMTP), Slack Webhook, Matrix Hookshot Webhook, and Generic HTTP Webhooks | ElastAlert 2 alerter modules (configured via the Configuration screen) |
| **Scheduling** | Reusable Activation Schedules with timezone/DST awareness, recurrence rules, and blackout/holiday exclusions | Cron-style execution rules defined per ElastAlert rule file |
| **Management** | Dedicated web UI under **Administration** -> **Notifications** (`/#/notifications`) | Key-value settings in **Administration** -> **Configuration** |

While ElastAlert 2 has historically handled outbound alerting for Sigma detections, the new Notifications module centralizes alerting across all Security Onion subsystems into a unified destination framework. In an upcoming releases, additional areas of Security Onion will utilize the new notifications module.

For details on configuring legacy detection alerters with ElastAlert 2, refer to the [ElastAlert 2 Notifications](elastalert-notifications.md) section.

## Using the Built-In SOC Notification Popup

In addition to routing notifications to external channels like email or chat webhooks, Security Onion includes a built-in notification popup panel accessible directly from the top navigation bar in the Security Onion Console (SOC). This panel provides real-time in-app alerting with per-user tracking of read and dismissed states.

### Notification Bell and Unread Badge

The notification icon is located in the upper-right corner of the SOC top navigation bar:

- **Unread Notifications**: When you have one or more unread notifications, a red badge appears on the envelope icon, and the icon displays as a closed envelope (`fa-envelope`).
- **All Read**: When all active notifications have been read, the red badge clears, and the icon displays as an open envelope (`fa-envelope-open`).
- **Opening the Popup**: Click the envelope icon to toggle the notification popup panel. The panel header displays the total count of loaded notifications (e.g., `Notifications (5)`).

### Viewing and Expanding Notifications

Notifications are displayed chronologically, with the newest alerts appearing first. Each notification item includes:

- **Severity Badge**: Color-coded text indicating the severity level (`CRITICAL`, `HIGH`, `MEDIUM`, `LOW`, or `INFO`).
- **Timestamp**: Relative time since the alert was generated (e.g., "5 minutes ago", "2 hours ago").
- **Read / Unread Styling**: Unread notifications are highlighted with a distinct background color and bold text, while read notifications appear in standard weight.
- **Title and Summary**: Displays the headline and summary description of the event.
- **Expand / Collapse**: Click anywhere on a notification item to expand or collapse it:
  - **Summary**: Full narrative description of the triggered event.
  - **Contextual Fields**: Displays key-value event attributes (e.g., node name, source IP, or metric details).
  - **Deep Links**: Pivot buttons allowing one-click navigation to relevant SOC views (such as Alerts, Hunt, Dashboards) or direct report downloads.

### Notification Actions

Each notification card provides individual action controls:

- **Mark as Read / Mark as Unread**: Click **Mark as Read** to acknowledge an alert and update your unread count badge. Once marked as read, you can click **Mark as Unread** if you wish to return it to the unread state.
- **Dismiss**: Click **Dismiss** to remove the notification from your active notifications list. Dismissed alerts no longer count toward your unread total and are hidden from the default view.
- **Restore**: If you are viewing dismissed notifications (see Options Menu below), click **Restore** to return a previously dismissed notification to your active notifications list.
- **User Activity Audit**: Users with the `superuser` or `auditor` role (`notifications/read_all`) can hover over the info icon (`fa-info-circle`) to view an audit popover showing which users have read or dismissed the notification, along with timestamps for each user action.

### Options Menu

Click the vertical ellipsis (**...**) icon in the upper-right corner of the notification card header to open the options menu:

- **Refresh**: Manually re-queries the backend and updates the notification list and unread count.
- **Show Dismissed / Hide Dismissed**: Toggles between displaying active notifications and previously dismissed notifications. When viewing dismissed notifications, items appear dimmed and include a **Restore** button.
- **Mark All as Read**: Marks all loaded notifications as read for your account in a single action.
- **Dismiss All**: Dismisses all active notifications for your account simultaneously.

### Per-User State and Retention Pruning

- **Independent User States**: Read and dismissed states are tracked per user. Under normal day-to-day operation, marking an alert as read or dismissing it only affects your personal view and does not immediately hide or clear the notification for other analysts.
- **Retention Pruning Behavior and Global Impact**: 
  A background cleanup process runs daily to prune old dismissed notifications. If **at least one user** has dismissed a notification and that dismissal reaches the retention cutoff window (by default, 30 days, configurable via the `soc.config.server.modules.notification.dismissedPruneDays` setting), the parent notification record is permanently removed.

    Because the notification record is removed globally with cascading deletion of all associated user states, **this pruning affects all users across the entire grid**:
    - Even if another user has **not dismissed** the notification—or has **not yet read** it—the notification will be completely removed from their view once pruned.
    - All per-user read and dismissal audit tracking for that notification is permanently removed.
    - If your organization requires extended visibility across multiple shifts or audit retention, increase the retention window by configuring `dismissedPruneDays` in [Administration](administration.md) -> Configuration (`SOC > config > server > modules > notification > dismissedPruneDays`).


## Accessing the Notifications Administration Screen

The Notifications administration interface is located in the Security Onion Console at **Administration** -> **Notifications** (`/#/notifications`).

### Access Requirements

Access to the Notifications administration interface is restricted to users who satisfy the following requirements:

1. **Active License**: The grid must have an active Security Onion Pro license with the notifications feature enabled (`FEAT_NTF`).
2. **Superuser Role**: The user must be assigned the `superuser` role.

Because notification destinations and activation schedules are persisted centrally in the Grid Pillar configuration store (`soc.config.server.modules.notification.destinations` and `soc.config.server.schedules`), managing them requires the `config/write` privilege (*Modify and synchronize Grid config*), which is held exclusively by superusers. Non-superuser roles cannot see the **Notifications** navigation item in the Administration menu and cannot modify destinations or schedules.

### User Roles and Notification Permissions

Security Onion uses [Role-Based Access Control (RBAC)](rbac.md) to govern notification interactions across different user personas:

- **`superuser`**: Has full administrative control to add, edit, and remove destinations and schedules (`config/write`), send ad-hoc and test notifications (`notifications/write`), view notifications and user audit logs (`notifications/read`, `notifications/read_all`), and toggle read or dismissed states.
- **`analyst` and `limited-analyst`**: Possess the `notifications/write` privilege, allowing them to dispatch ad-hoc manual notifications via the API or command line and manage their personal read/dismissed states in the SOC Bell panel. However, they cannot modify administrative destinations or schedules.
- **`auditor` and `limited-auditor`**: Possess the `notifications/read` privilege, enabling them to view their personal notifications in the SOC Bell panel. Full auditors additionally possess `notifications/read_all`, enabling them to review audit history of which users viewed or dismissed specific notifications.

## Severities and Activation Schedules

Notifications utilize two primary filtering mechanisms to ensure that alerts reach the appropriate teams at the proper times: **Severities** and **Activation Schedules**.

### Severities

Every notification generated in Security Onion is assigned one of five standardized severity levels:

- `critical`
- `high`
- `medium`
- `low`
- `info`

Destinations can be configured to restrict incoming notifications to specific severity levels:

- **All Severities**: If no severities are selected for a destination (the severities list is empty), the destination accepts notifications of all severity levels.
- **Filtered Severities**: When one or more severities are selected (e.g., `high` and `critical`), the destination will only deliver notifications matching those specified severities. Any notification with a non-matching severity is automatically dropped for that destination during transmission.

This allows organizations to direct low-priority informational messages (such as completed PCAP exports or daily reports) to internal channels or email distribution lists, while routing high and critical infrastructure alarms to real-time messaging channels or on-call webhooks.

### Reusable Activation Schedules

Activation schedules define the time windows during which notification destinations are permitted to transmit alerts. Schedules are managed independently under the **Activation Schedules** tab and can be reused across any number of destinations.

Key features of activation schedules include:

- **Recurrence Types**:
  - **Daily**: Runs every day within specified time windows (e.g., `17:00` to `08:00` for overnight monitoring).
  - **Weekly**: Runs on selected days of the week (e.g., Monday through Friday, or Saturday and Sunday).
  - **Monthly**: Runs on specific days of the month (e.g., the 1st and 15th, or `-1` for the last day of the month) or ordinal weekdays (e.g., the 1st and 3rd Tuesday).
  - **Annually**: Runs on specific dates or ordinal occurrences in designated months (e.g., the fourth Thursday in November).
- **Time Windows & All Day**: Each definition within a schedule can span a specific time window (`HH:MM` in 24-hour notation, supporting overnight windows crossing midnight) or be set to **All Day** (24 hours).
- **Timezone Awareness**: Each schedule specifies an IANA timezone identifier (e.g., `America/New_York`, `UTC`, `Europe/London`). The evaluation engine automatically accounts for local Daylight Saving Time (DST) shifts without manual adjustments.
- **Exception & Blackout Schedules**: A schedule can reference one or more **Exception Schedules** (e.g., company holidays or maintenance windows). If an excluded exception schedule is currently active, the parent schedule is suppressed and considered inactive. Circular references between schedules are proactively prevented using Directed Acyclic Graph (DAG) validation.
- **Multi-Schedule Assignment**: A destination can be linked to multiple activation schedules. If multiple schedules are assigned, the destination operates under logical OR semantics: the destination is active if **any** of its assigned, enabled schedules is currently active.
- **Always Active Fallback**: Destinations without any assigned schedules are considered **Always Active** and will deliver notifications at all times.
- **Bypassing Schedules**: When sending manual or test notifications, administrators can optionally select **Bypass Schedules** to force immediate delivery regardless of active schedule windows.


## When Destinations are Utilized in a Notification Transmission

When any Security Onion subsystem dispatches a notification—or an operator initiates a manual notification—the notification engine executes a sequential evaluation pipeline for each destination.

A destination will receive and transmit the notification only when all of the following criteria are satisfied:

```
+-------------------------------------------------------------+
|                Notification Dispatch Request                |
|           (Source: Grid Metric, PCAP, Report, Client)       |
+------------------------------+------------------------------+
                               |
                               v
               +-------------------------------+
               | 1. License & Subsystem Active?| -----> No: Abort dispatch
               +---------------+---------------+
                               | Yes
                               v
               +-------------------------------+
               | 2. Destination Targeted?      | -----> No: Skip destination
               |    (Explicit ID or Broadcast) |
               +---------------+---------------+
                               | Yes
                               v
               +-------------------------------+
               | 3. Destination Enabled?       | -----> No: Skip destination
               +---------------+---------------+
                               | Yes
                               v
               +-------------------------------+
               | 4. Severity Matched?          | -----> No: Skip destination
               |    (Matches list or All)      |
               +---------------+---------------+
                               | Yes
                               v
               +-------------------------------+
               | 5. Schedule Active or         | -----> No: Skip destination
               |    Bypass Schedules Enabled?  |
               +---------------+---------------+
                               | Yes
                               v
               +-------------------------------+
               | 6. Recipient Targeting Check  |
               |    (Evaluate skipIfRecipients)|
               +---------------+---------------+
                               | Passed
                               v
               +-------------------------------+
               | 7. Adapt Payload Capabilities |
               |    (Attachments, Deep-Links)  |
               +---------------+---------------+
                               |
                               v
               +-------------------------------+
               | 8. Check Silence/Debounce     | -----> Suppressed: Skip
               +---------------+---------------+
                               | Passed
                               v
               +-------------------------------+
               | 9. Deliver via Channel Driver |
               |    (SOC, SMTP, Slack, Webhook)|
               +-------------------------------+
```

1. **Subsystem & License Check**: The grid must possess an active Security Onion Pro license, and the notification engine must be enabled. If unlicensed or disabled, dispatch is halted.
2. **Destination Target Matching**:
   If an explicit destination ID is targeted (such as when testing a specific destination from the UI), only that destination is processed.
   If no destination ID is provided (a global dispatch), all configured destinations in the system are evaluated.
3. **Enabled Status**: The destination must have its `enabled` toggle set to `true`. Disabled destinations are skipped.
4. **Severity Evaluation**: If the destination specifies a list of allowed severities, the notification's severity must match one of them (case-insensitive). If no severities are configured on the destination, all severities are accepted.
5. **Schedule Evaluation**:
   If `Bypass Schedules` is set on the notification payload, schedule checks are skipped.
   Otherwise, if the destination has one or more linked schedules, at least one enabled schedule must be active at the current moment (taking into account time of day, day of week, timezone, and exception blackout schedules). If none of the linked schedules are active, the destination is skipped.
   - If no schedules are assigned to the destination, it is always active.
6. **Recipient Targeting Evaluation**:
   When a notification targets specific users:
     If the destination driver supports recipients (e.g., SOC Bell panel or SMTP) and `Enable Recipients` is active, the notification is routed to those specific users.
     If the channel driver does not support recipients or recipient targeting is disabled:
     If `Skip If Recipients` is enabled on the destination, the transmission to this destination is skipped.
     If `Skip If Recipients` is disabled, the recipient filter is stripped for this destination, broadcasting the notification to the destination's default audience.
7. **Capability Adaptation**: Payload features are adjusted to match the channel driver's capabilities:
   Attachments (such as generated PDF or CSV reports) are attached or linked based on driver support and configuration.
   Deep links back to SOC dashboards or investigation views are adapted for channels supporting markdown or hypertext links.
8. **Silencing & Debouncing**: If the caller supplied silencing parameters (`SilenceKey`, `SilenceDuration`, `ThresholdCount`), repeat triggers within the silence window are debounced to prevent alert floods.
9. **Transmission**: The prepared payload is delivered to the channel driver for final transmission.


## Managing Notification Destinations

The **Destinations** tab under **Administration** -> **Notifications** displays all configured destination channels, their channel types, allowed severities, assigned activation schedules, active schedule status, and operational controls.

### Adding a Destination

To create a new notification destination:

1. In SOC, navigate to **Administration** -> **Notifications**.
2. On the **Destinations** tab, click the **+** (Add Destination) button on the toolbar.
3. In the dialog, configure the destination properties:
  - **Name**: Enter a human-readable display name (up to 50 characters).
  - **Channel Type**: Select the channel driver type. Supported drivers include:
    - **SOC Notification Bell (`soc`)**: Routes alerts to the built-in top-bar bell panel in the SOC interface, persisting notifications with read/unread tracking.
    - **Email (SMTP) (`smtp`)**: Sends emails via an external SMTP server. Configure **SMTP Host**, **Port** (e.g., 25, 587, or 465), **From Address**, optional default **To Addresses** (comma-separated), optional **Username** and **Password**, **Attachment Mode** (`both`, `attach`, or `link`), **Use TLS**, and **Insecure Skip Verify** (to bypass TLS verification for self-signed certificates). Note that From Address can be entered as `Display Name <some@user.invalid>` to include a human readable email source.
    - **Slack Webhook (`slack_webhook`)**: Delivers formatted notification cards to a Slack channel using an Incoming Webhook URL.
    - **Matrix Hookshot Webhook (`matrix_hookshot_webhook`)**: Delivers alerts to Matrix rooms via the Matrix Hookshot webhook bridge.
    - **Generic Webhook (`generic_webhook`)**: Sends HTTP POST requests containing structured JSON notification payloads to any arbitrary webhook endpoint.
  - **Enabled**: Toggle whether this destination is actively receiving alerts.
  - **Enable Recipients**: For drivers supporting recipient targeting (SOC Bell and SMTP), toggle whether notifications targeted to specific user IDs should be delivered to those users.
  - **Skip If Recipients**: When recipient targeting is unsupported or disabled, choose whether to skip sending targeted notifications to this destination.
  - **Severities**: Select the severity levels (`critical`, `high`, `medium`, `low`, `info`) to accept. Leave blank to allow all severities.
  - **Activation Schedules**: Select one or more reusable activation schedules. Leave blank to keep the destination always active.
4. Click **Save** to persist the destination.

### Updating a Destination

To modify an existing notification destination:

1. Navigate to **Administration** -> **Notifications** -> **Destinations**.
2. In the destinations table, locate the target destination and click the **Pencil** (Edit) icon in the Actions column.
3. Update any destination settings, parameters, allowed severities, or linked activation schedules.
   - For SMTP destinations with existing passwords, the password field displays a masked value (`******`). If left unchanged, the existing password is preserved securely.
4. Click **Save** to apply the updates.

### Removing a Destination

To delete a notification destination:

1. Navigate to **Administration** -> **Notifications** -> **Destinations**.
2. In the destinations table, click the **Trash** (Delete) icon on the row of the destination you wish to remove.
3. Review the confirmation dialog warning that subsequent alerts routed to this destination will no longer be delivered.
4. Click **Delete** to permanently remove the destination.

!!! NOTE

    The default built-in SOC Notification Bell (`soc-bell`) cannot be deleted. If you do not wish to use the built-in bell panel, you can disable it by editing the destination and setting the **Enabled** toggle to off.


## Sending Manual and Test Notifications

The Notifications module provides capabilities for sending ad-hoc broadcast notifications across all destinations or executing targeted tests against specific destination channels.

### Sending Global Manual Notifications

Global manual notifications allow administrators to broadcast custom messages across all configured notification channels (e.g., for system maintenance announcements, test drills, or incident notifications):

1. Navigate to **Administration** -> **Notifications** and select the **Destinations** tab.
2. Click the **Paper Plane** (Send Notification) button located on the top toolbar.
3. Fill out the notification form:
   - **Title**: Enter the notification headline (required, up to 255 characters).
   - **Summary**: Enter the detailed description or body text (up to 4,000 characters).
   - **Severity**: Select the severity level (`info`, `low`, `medium`, `high`, or `critical`). The notification will only be transmitted to destinations that accept this severity level.
   - **Recipients**: Optionally select specific SOC user accounts to receive a targeted notification. If left empty, the notification broadcasts to all users.
   - **Bypass Schedules**: Check this option to immediately send the notification to all eligible destinations regardless of whether their linked activation schedules are currently active.
4. Click **Send**.
5. The system dispatches the notification and displays a confirmation indicating how many destinations successfully transmitted the alert.

### Sending Specific Destination Manual / Test Notifications

To verify channel credentials, webhook endpoints, or notification formatting for an individual destination:

1. Navigate to **Administration** -> **Notifications** and locate the desired destination in the table.
2. In the Actions column for that row, click the **Paper Plane** (Send Notification) icon.
3. The dialog opens pre-configured for that destination, displaying a title such as `Send Notification to <Destination Name>`.
4. The **Title** field is automatically populated with a test title (e.g., `Test Notification (<Destination Name>)`), and the **Summary** contains standard test verification text. You may customize these fields if desired.
5. If testing an out-of-schedule destination, ensure the **Bypass Schedules** toggle is enabled so delivery is not blocked by schedule restrictions.
6. Click **Send**.
7. If the transmission succeeds, a confirmation message appears. If the remote service rejects the notification (e.g., due to invalid SMTP credentials, an invalid webhook URL, or network connectivity issues), an error message with diagnostic details is displayed.

