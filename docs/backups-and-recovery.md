# Encrypted backups and recovery readiness

## Problem

Small always-on machines are inexpensive and useful, but storage failure, accidental changes and corruption can still destroy application state. For selected stateful workloads, I implemented scheduled local snapshots plus encrypted off-site backups.

This page documents the **design and checks**, not the private backup configuration or any recovery credentials.

## Implemented workflows

| Workload | Backup frequency | Local retention | Off-site retention | Verification |
|---|---|---|---|---|
| Uptime Kuma | Daily | 7 days | 30 days | SQLite integrity and remote content comparison |
| LastZ beta bot data | Hourly | 7 days | 30 days | SQLite integrity and encrypted remote upload |

The LastZ workload shown here is the **beta instance** on the Armbian box. No separate production instance is implied.

Not every service is backed up by this workflow. For example, the current AdGuard Home configuration has deliberately not been included in the off-site backup set. This is a conscious scope limitation, not a claim of full-host disaster recovery.

## Uptime Kuma backup pipeline

~~~mermaid
flowchart LR
    A["Live SQLite database"] -->|"SQLite Online Backup API"| S["Consistent temporary snapshot"]
    S --> I["PRAGMA integrity_check"]
    I -->|"Pass"| P["Compressed archive<br/>DB + required app files"]
    P --> L["Local retention"]
    P --> C["rclone crypt"]
    C --> B["Backblaze B2 storage"]
    B --> V["Remote content verification"]
    V --> N["Scheduled job status / failure alert"]
~~~

### Why these choices?

**Application-consistent SQLite snapshot:** copying a live database file without accounting for active writes can yield a bad backup. SQLite's Online Backup API gives a consistent snapshot while the application remains available.

**Integrity check:** a successfully copied file is not necessarily a healthy database. SQLite integrity checking is part of the pipeline.

**Off-site encrypted destination:** rclone crypt encrypts the data before it reaches B2. Authentication details and crypt configuration stay on the host, not in source control.

**Remote verification:** checking the upload against the retained local copy detects transfer or storage mismatch. A successful remote check is valuable evidence, but it does **not** equal a successfully tested application restore.

**Independent failure reporting:** the backup jobs run from systemd timers / services. A failure can trigger a separate Discord alert path.

## Reliability practices used

- Scheduled jobs rather than keeping a separate backup process permanently running.
- Restricted filesystem permissions for sensitive local configuration.
- Local retention to limit disk growth.
- Remote retention scoped to specific backup object naming and location.
- Verification before treating a newly created archive as complete.
- Backup execution and failure handling visible through systemd/journald.

## Real execution evidence

A manually executed backup on **2026-11-18** reported `SQLite integrity: ok`, `0 differences found`, `1 matching files`, and successful completion. The identifiers have been redacted in the [verification transcript](evidence/uptime-kuma-backup-verification-2026-11-18.md). This is evidence of a successful backup-and-compare run, **not** a tested full restore.

## Recovery status

**Completed:** automated snapshots, database integrity checks, encrypted remote upload, and remote verification.

**Not claimed:** a fully tested disaster recovery plan or successful bare-metal restore. A restore drill remains a meaningful future milestone.

### Planned restore drill

1. Provision a clean, isolated test environment.
2. Retrieve a chosen encrypted archive with authorized credentials.
3. Extract only in the test environment.
4. Check the restored SQLite database.
5. Start the application with restored state and validate its functionality.
6. Record recovery time, missing dependencies and corrective actions.

This is a **planned validation procedure**, not a report of steps already completed.

## Publication safety

Do not commit any of the following:
- rclone remote credentials or crypt passwords
- B2 access keys, bucket-level permission data or signed URLs
- Discord webhook URLs or Uptime Kuma push URLs
- backup archives, application databases or real config files with secrets
- actual paths that identify private mounted locations unnecessarily

[See monitoring](monitoring.md) · [See architecture](architecture.md) · [Back to README](../README.md)
