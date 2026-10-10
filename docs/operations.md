# Routine operations and backup verification

[← README](../README.md) · [Installation](installation.md) · [Backup format](backup-format.md)

## Manual backup

Run as root on a standalone node **after all guests have stopped**:

```bash
/usr/local/sbin/pve-config-backup
```

The script performs its own fail-closed checks, acquires `/run/lock/pve-config-backup.lock`, creates a temporary working set, obtains a quiescent `pmxcfs` snapshot, archives host paths, records diagnostics, and verifies the result before placing it under its final timestamped directory.

**During execution, `pve-cluster` is stopped and restarted.** Even a successful run is not “no downtime”; choose a maintenance window. If recovery of the service fails, the script preserves incomplete evidence and stops the automatic backup timer. Use local console; do not delete the evidence or restart services blindly.

## Verify the newest finalized backup

A conservative check is to **select a specific backup path** from the output of a successful run rather than guessing which directory is newest. Substitute the actual finalized directory:

```bash
BACKUP='/var/lib/vz/host-config-backups/YYYY-MM-DD_HH-MM-SS'
test -f "$BACKUP/VERIFIED" && grep -x 'Status: OK' "$BACKUP/VERIFIED"
( cd "$BACKUP" && sha256sum -c SHA256SUMS )
sqlite3 "$BACKUP/config.db" 'PRAGMA integrity_check;'
tar -tzf "$BACKUP/etc-pve.tar.gz" >/dev/null
tar -tzf "$BACKUP/etc-host.tar.gz" >/dev/null
```

Expected: `Status: OK`, six SHA-256 `OK` entries, SQLite `ok`, and successful tar listings. This is **integrity/readability verification**, not a guarantee of successful disaster recovery.

The restore program's option **1** performs additional metadata and safety verification of its selected backup and validates the SQLite database using a private disposable copy. A backup must match the current node's hostname; this is **not** a generic cross-host migration tool.

## Scheduling and retention

Defaults in backup 1.6: 30 days retention and a minimum of ten newest `VERIFIED` backups protected regardless of age. Incomplete stale `.YYYY-MM-DD_HH-MM-SS.tmp.*` directories older than one day may be deleted by a subsequent successful backup's retention phase. A temporary work directory associated with failed `pmxcfs` recovery is preserved for diagnosis at failure time; **do not depend on indefinite retention of stale work directories**.

The example timer triggers at 03:30 local time. Because active guests are deliberately refused, **daily trigger ≠ daily successful backup**. Consider scheduled guest shutdowns only as a separately managed operational decision; these scripts do not shut down guests for you.

## Storage and privacy

Backup output includes `/etc` (excluding the live `/etc/pve` mount), `/etc/pve` as a separate archive, the `pmxcfs` database, system state, and potentially SSH material, private keys, and `/etc/shadow`. Root-only filesystem permissions protect against casual disclosure but are not encryption. Treat any copy, snapshot, archive, attached support log, or off-host export as confidential.

## When something fails

1. Retain the exact terminal output and `journalctl -b -u pve-cluster` as private incident evidence; inspect for secrets before sharing.
2. Verify whether `/etc/pve` is mounted and `pve-cluster` is healthy before rerunning. If the script marked the situation CRITICAL, follow that recovery instruction first.
3. Do not remove a runtime mask or restart a stopped backup timer until the affected database and Proxmox state are understood.
4. Do not upload actual backup data to public GitHub issues.

See [the recovery runbook](recovery-runbook.md).
