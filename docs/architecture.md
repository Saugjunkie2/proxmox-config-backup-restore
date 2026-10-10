# Architecture

[← README](../README.md) · [Backup format](backup-format.md) · [Security](security-model.md)

```text
    Proxmox standalone node (root)
       |
       +-- /usr/local/sbin/pve-config-backup v1.6
       |     |-- safety gates: node/guests/services/source paths
       |     |-- exclusive backup lock
       |     |-- stable /etc/pve static archive + manifest
       |     |-- controlled pve-cluster stop/runtime mask
       |     |-- SQLite snapshot + static state comparisons
       |     |-- restart and verify pve-cluster
       |     |-- /etc and selected host paths + diagnostics
       |     +-- checksums + DB/tar verification + VERIFIED + retention
       |                  |
       |           secure local backup directory
       |                  |
       +-- /usr/local/sbin/pve-config-restore v1.14
             |-- selected backup checks / node identity
             |-- interactive menu (read-only vs mutating)
             |-- restore lock + backup timer coordination
             |-- pin selected backup + optional pre-restore backup
             |-- domain-specific stage, validation, apply/rollback
             +-- critical/manual-recovery latch on uncertain outcomes
```

## Separation of responsibilities

**Backup** is a producer of complete local host-configuration recovery sets. **Restore** is the consumer of one selected set. The two programs expect the same base directory and payload conventions. Do not treat them as independent formats.

`/etc/pve` is served by `pmxcfs`, backed by `/var/lib/pve-cluster/config.db`. The source is not a normal persistent folder. Backup 1.6 therefore attempts a coherent snapshot using a controlled `pve-cluster` service interruption, rather than blindly copying a live SQLite database.

Restore 1.14 has multiple independent mutation domains. Some use staged file replacement, native PVE locks, snapshots of PREVIOUS/TARGET states, conditional validation, and rollback. The SSH mode embeds a substantial Python transaction helper. The PVE identities and storage modes call native Proxmox Perl APIs. The network mode uses `ifupdown2` and optionally a constrained management-connectivity check.

## Transaction and recovery boundaries

- **Per-process state is not durable crash recovery.** In-process unexpected errors and signal handling are not equivalent to persistence across `SIGKILL`, sudden power loss or reboot.
- **Rollback is conditional, not guaranteed.** A failed or ambiguous rollback may leave evidence and stop the timer for manual recovery.
- **Access infrastructure is in scope.** Network, SSH and firewall restoration can make remote administration unavailable even if file writes succeed.
- **Emergency `config.db` restore is not a selective restore.** It replaces the entire persisted `pmxcfs` configuration and attempts reboot; changes after the backup may disappear from Proxmox's configuration inventory.

## System integration

Default paths:

| Path | Function |
| --- | --- |
| `/usr/local/sbin/pve-config-backup` | Installed backup command |
| `/usr/local/sbin/pve-config-restore` | Installed restore command |
| `/var/lib/vz/host-config-backups` | Root-private backup base |
| `/run/lock/pve-config-backup.lock` | Backup exclusion |
| `/run/lock/pve-config-restore.lock` | Restore exclusion |
| `/etc/pve` | Live `pmxcfs` configuration mount |
| `/var/lib/pve-cluster/config.db` | Persistent `pmxcfs` database |
| `/etc/systemd/system/pve-config-backup.{service,timer}` | Optional example scheduling deployment |

The scripts may create additional per-domain temporary files, protected working directories and lock files. Consult their source and runtime diagnostic output before manual cleanup.
