# Backup format and integrity contract

[← README](../README.md) · [Architecture](architecture.md)

Each finalized backup is a timestamp-named directory under `/var/lib/vz/host-config-backups`. The actual files created by backup version 1.6 are:

| File | Purpose | Covered by `SHA256SUMS`? |
| --- | --- | :---: |
| `backup-info.txt` | Source node, script version, start date, destination, snapshot provenance and attempts | Yes |
| `config.db` | Quiescent SQLite copy of the persistent `pmxcfs` database | Yes |
| `etc-pve.tar.gz` | Readable archive of the `pmxcfs` `/etc/pve` tree | Yes |
| `etc-host.tar.gz` | Host-side `/etc`, optional selected `/usr/local/lib/systemd` paths and optional restore executable | Yes |
| `pve-config-backup.sh` | Copy of the exact backup script used to create the set | Yes |
| `system-state.txt` | Host, PVE, package, network, firewall, service and other diagnostics | Yes |
| `SHA256SUMS` | Six SHA-256 records for the files above | No (cannot self-hash) |
| `VERIFIED` | Human-readable success marker, including `Status: OK` and node | No |

## Snapshot characteristics

Version 1.6 rejects active guests and cluster configuration. It captures static `/etc/pve` content, stops and temporarily runtime-masks `pve-cluster`, checks `config.db` and SQLite sidecars, creates the database snapshot, compares persistent/static views before/after, then validates the restarted service. It permits bounded retries on particular failures; a failed workflow does **not** become a final `VERIFIED` backup.

Do not describe `config.db` and `etc-pve.tar.gz` as redundant copies with identical restore semantics. The former is a full persistent database state; the latter supports inspection and targeted file recovery.

## Scope

`etc-host.tar.gz` covers host `/etc` while excluding `/etc/pve` (stored separately). It may also cover `/usr/local/lib/systemd/network`, `/usr/local/lib/systemd/system`, and the installed `/usr/local/sbin/pve-config-restore` if present. It does **not** promise entire `/usr/local`, VM disk volumes, ZFS datasets, or external storage.

Some Proxmox certificate/key and PVE identity source paths are **mandatory** for the script's final checks. Missing mandatory paths cause a backup to fail rather than producing an incomplete success marker.

## Verification and threat model

- A successful backup requires checksums, SQLite `PRAGMA integrity_check`, tar readability and mandatory archive members.
- The restore tool checks `VERIFIED`, safe manifest paths, six required payload entries, node identity, checksums, tar readability and a private SQLite copy.
- A self-contained checksum manifest does **not** provide authenticity against an adversary who can change both data and checksums. Protect backups, their parent directory and independent copies.
- The selected node name is part of the restore admission contract: do not rename the backup node field to bypass it.
- Treat `system-state.txt` as potentially confidential; it may contain interface addresses, software inventory, listening ports, principals and other infrastructure detail.

## Retention

Default 30-day age retention and ten newest verified backups protected, not ten backup sets kept as an absolute upper bound. The script also cleans certain orphan temporary work directories after one day on subsequent runs. Existing backup directories should not be manually renamed, moved into unsuitable paths, or edited in place and then treated as verified.
