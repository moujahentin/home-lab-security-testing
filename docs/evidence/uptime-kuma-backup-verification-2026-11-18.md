# Evidence: successful Uptime Kuma backup verification

**Captured:** 2026-11-18 (EET)  
**Workload:** Uptime Kuma on the Armbian monitoring node  
**Evidence source:** Console output shared from a manually executed backup job  
**Status observed:** Backup completed; SQLite integrity and remote comparison reported success.

## Redacted execution transcript

The following is a **sanitized transcription of a real terminal result**. Only local/remote storage identifiers have been replaced; the reported test outcomes are preserved.

```text
SQLite integrity: ok
Backup archive: [REDACTED LOCAL PATH]/kuma_20261118_110059.tar.gz
2026/11/18 11:01:09 NOTICE: [REDACTED ENCRYPTED REMOTE]: 0 differences found
2026/11/18 11:01:09 NOTICE: [REDACTED ENCRYPTED REMOTE]: 1 matching files
Kuma backup completed successfully.
```

## What the result supports

1. The backup process produced a local archive and printed a successful SQLite integrity result.
2. The remote comparison returned **0 differences** for the selected archive.
3. The comparison reported **1 matching file**, consistent with one backup object being checked.
4. The backup script completed without reporting an error in this execution.

## What it does *not* prove

- A new server has been restored from the archive and started successfully.
- Every historical backup in the bucket remains intact.
- The machine has high availability or a guaranteed recovery time.
- The encrypted archive can be recovered without the separately protected crypt and storage credentials.

A **restore drill** remains a separate planned activity.

## Privacy and reproducibility

The transcript was taken from an actual homelab session, then redacted before publication. This page does **not** include cloud bucket names, access keys, remote aliases, live host addresses, database files, or raw backup archives.

The implementation uses SQLite-aware snapshots and a remote comparison stage; see the [backup design](../backups-and-recovery.md) for context.

[Back to the portfolio](../../README.md)
