# Time Zones

When you run Security Onion Setup, it sets the operating system time zone to UTC/GMT. Logging in UTC is considered a best practice across the cybersecurity industry because it makes it that much easier to correlate events across different systems, organizations, or time zones. Additionally, it avoids issues with time zones that have daylight savings time which would result in a one-hour time warp twice a year. 

Web interfaces like [Alerts](alerts.md), [Dashboards](dashboards.md), [Hunt](hunt.md), and [Kibana](kibana.md) should try to detect the time zone of your web browser and then render those UTC timestamps in local time. [Alerts](alerts.md), [Dashboards](dashboards.md), and [Hunt](hunt.md) allow you to manually set your time zone under Options.

For more information about the operating system time, please see the [NTP](ntp.md) section.