# Evidence: systemd timer automation audit (2026-11-18)

**Date:** 18 November 2026 (EET)  
**Environment:** ARM-based Armbian homelab node  
**Evidence:** owner-supplied, read-only terminal output showing scheduled systemd timers, enabled/active timer units and the latest backup service results.

## What was implemented

Rather than keeping an additional scheduling daemon running for every automation task, the node uses **systemd timer + oneshot service** pairs.

| Timer / service pair | Observed schedule | Purpose |
|---|---|---|
| `kuma-check-discord-bot.timer` | Approximately every minute | Locally check a Discord background worker and report to an Uptime Kuma push monitor |
| `kuma-check-lastz-bot.timer` | Approximately every minute | Locally check the **LastZ BETA** worker and report to its separate push monitor |
| `lastz-beta-heartbeat.timer` | Approximately every 5 minutes | Send an outbound heartbeat for **external host liveness**; it is *not* a direct functional test of the bot |
| `lastz-beta-backup.timer` | Hourly | Back up the LastZ **BETA** application's data |
| `kuma-backup.timer` | Daily at 03:30 local time | Back up Uptime Kuma application data |

The schedule descriptions are derived from the systemd `NEXT` and `LAST` output at the inspection time. They are *not* measurements of scheduling reliability over months.

## Observed status — sanitized terminal transcript

The relevant values from the owner's terminal were:

```text
=== AUTOMATION TIMERS ===
UNIT                              LAST (EET)            NEXT (EET)
kuma-check-discord-bot.timer      2026-11-18 12:23:50   2026-11-18 12:24:51
kuma-check-lastz-bot.timer        2026-11-18 12:23:50   2026-11-18 12:24:51
lastz-beta-heartbeat.timer        2026-11-18 12:23:45   2026-11-18 12:28:45
lastz-beta-backup.timer           2026-11-18 12:00:02   2026-11-18 13:00:00
kuma-backup.timer                 2026-11-18 03:30:01   2026-11-19 03:30:00

=== BACKUP TIMERS ===
kuma-backup.timer
enabled
active
lastz-beta-backup.timer
enabled
active

=== MOST RECENT RECORDED SERVICE RESULTS ===
Id=kuma-backup.service
Result=success
ExecMainStatus=0

Id=lastz-beta-backup.service
Result=success
ExecMainStatus=0
```

The original output was lightly reformatted into aligned columns. No host addresses, secrets, backup destination identifiers or webhook URLs were included.

## What this evidence demonstrates

1. **Scheduled automation exists:** systemd showed five named timers with recent and upcoming trigger times.
2. **Backup schedules are enabled:** both backup timers reported `enabled` and `active`.
3. **Recent backup execution did not report a failure:** each backup service reported `Result=success` and process exit code `0` for its most recent recorded invocation.
4. **Different checks have different meanings:** push monitors are tied to local service checks, while the external heartbeat sends a liveness signal for the host.

## What it does *not* prove

- That every future timer execution will succeed.
- That the off-site backup contents are valid solely because `systemd` returned success. Separate SQLite integrity and remote comparison results are documented in the [backup verification evidence](uptime-kuma-backup-verification-2026-11-18.md).
- That a restored application has been successfully started on another host; a restore drill remains outstanding.
- That an external heartbeat independently checks the LastZ application.

## Engineering takeaway

The combination of a lightweight scheduling mechanism, per-job exit status, external notifications and targeted integrity checks gives useful evidence for managing always-on services on resource-constrained hardware—while keeping clear boundaries between **scheduled**, **executed**, **verified** and **recoverable**.

[Monitoring design](../monitoring.md) · [Backup design](../backups-and-recovery.md) · [Portfolio](../../README.md)
