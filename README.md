# Proxmox Config Backup & Restore

**Proxmox VE host configuration backup and restore tools** for standalone nodes. Two complementary Bash utilities create verified local configuration backups and provide selective recovery of SSH, networking, firewall, storage, Proxmox accounts, and VM configuration—plus an emergency `pmxcfs` database restore.

**Repository:** `proxmox-config-backup-restore` · **License:** [MIT](LICENSE) · **Publisher:** [@Saugjunkie2](https://github.com/Saugjunkie2) · **Status:** frozen script versions, documentation-only publication.

> **Scope:** Proxmox VE **host configuration**, not Proxmox Backup Server (PBS), guest disk images, or VM data backups. The tools are intended for standalone hosts, not general cluster recovery.

- [`pve-config-backup`](scripts/pve-config-backup) — version **1.6**; builds and verifies a host-configuration backup, including a quiescent `pmxcfs` database snapshot.
- [`pve-config-restore`](scripts/pve-config-restore) — version **1.14**; verifies backup sets and offers targeted SSH, network, firewall, storage, PVE identity, and VM configuration recovery, plus an emergency full `config.db` replacement.

> **Important:** These are preserved production-origin script snapshots, **not** general-purpose VM-backup software. The released source is intentionally frozen, including historical comments and revision labels. Repository documentation does not change script behavior. Read [limitations](docs/limitations.md) and [security considerations](docs/security-model.md) **before** using either tool.

## Why these tools exist

- **Consistent `pmxcfs` snapshots:** the backup process checks guest activity, temporarily quiesces `pve-cluster`, captures `config.db`, then checks service recovery and backup integrity.
- **Targeted recovery:** use the interactive restore menu to recover a specific configuration domain rather than immediately reverting the entire Proxmox configuration database.
- **Explicit integrity records:** each completed backup set contains a SHA-256 manifest, SQLite integrity checks, archive checks and a `VERIFIED` marker; read the [limits of these checks](docs/backup-format.md).
- **Preserved source history:** the publication keeps both original scripts byte-for-byte, including historical development notes, while providing new English documentation and reproducible source checksums.

These are design characteristics from the shipped source, **not** a claim of independent security certification or broad compatibility. See [known caveats](docs/security-model.md#known-source-level-caveats).

## At a glance

| Capability | Backup 1.6 | Restore 1.14 |
| --- | --- | --- |
| Local Proxmox host configuration | Archives selected host paths and `/etc/pve` | Selective recovery from a verified set |
| Quiescent `pmxcfs` database | Creates and checks an offline-consistent database copy | Emergency replacement of complete `config.db` |
| Integrity | SHA-256, SQLite integrity, tar readability, required files | Re-verifies selected backup, metadata and database |
| Guest disk data | **Not backed up** | **Not restored** |
| Cluster operation | **Not supported** | At least the VM and full-database recovery paths refuse clusters; do not assume cluster-wide support for any other mode |
| Running guests | Backup refuses running QEMU/LXC guests | Recovery imposes operation-specific restrictions |
| Off-host disaster recovery | **Not provided** | **Not provided** |

## Read this first

1. Use a **standalone** host and retain **local/IPMI/physical-console access**. Recovery may interrupt SSH, networking, firewall services, or `pmxcfs`.
2. **Stop all QEMU VMs and LXC containers before creating a backup.** Backup 1.6 deliberately stops/restarts `pve-cluster` for a coherent `config.db` snapshot; its preflight refuses active guests.
3. Backup sets are placed in `/var/lib/vz/host-config-backups` (normally on the host filesystem). **They are not a substitute for external, offline or independent storage.** Protect them as secrets.
4. Verify backups and understand the selected restore mode before applying it. The emergency full-database mode **rolls back the entire Proxmox configuration and attempts an automatic reboot**.
5. The original scripts have **not been rewritten or translated**. New documentation is in English, but some source comments and historical revision notes remain in German.
6. There are [known, unresolved safety considerations](docs/security-model.md#known-source-level-caveats). Publication of a frozen snapshot does **not** imply universal compatibility or independent certification.

## Installation (manual, recommended starting point)

On the Proxmox host, using a **root shell**, from a checked-out repository:

```bash
cd /path/to/proxmox-config-backup-restore
sha256sum -c SHA256SUMS
install -d -o root -g root -m 0700 /var/lib/vz/host-config-backups
apt-get update
apt-get install sqlite3 python3
install -o root -g root -m 0700 scripts/pve-config-backup /usr/local/sbin/pve-config-backup
install -o root -g root -m 0700 scripts/pve-config-restore /usr/local/sbin/pve-config-restore
```

**Do not run the backup until the host meets the operational prerequisites.** Additional commands and packages are used by specific modes; both scripts check many requirements at runtime. See [the full installation guide](docs/installation.md).

For a first backup, once the environment is prepared and all guests are stopped:

```bash
/usr/local/sbin/pve-config-backup
```

For the interactive recovery menu:

```bash
/usr/local/sbin/pve-config-restore
```

Do **not** choose a mutation menu item merely to test it. Option **1** verifies the selected backup without restoring it.

## Recovery menu

| Option | Operation | Scope / warning |
| ---: | --- | --- |
| 1 | Verify selected backup | Read-only backup inspection; no restore |
| 2 | Restore SSH | Main `sshd_config` and drop-in configuration; can affect remote access |
| 3 | Restore network | `interfaces`, `interfaces.d`, selected link pinning; may disconnect SSH |
| 4 | Restore firewall | Node, datacenter, or both configuration files; may lock out access |
| 5 | Restore `storage.cfg` | Storage configuration, **not** storage contents |
| 6 | Restore PVE users / ACLs / passwords | Restricted, atomic PVE `user.cfg` + `priv/shadow.cfg` scope; **not** Linux passwords |
| 7 | Restore one VM configuration | QEMU config only; **not** guest disks |
| 8 | Emergency complete `config.db` | Destructive scope expansion across all `pmxcfs` configuration; reboot is attempted |
| 9 | Show backup information | Selected set's metadata |
| 10 | Select another backup | Switch verified set |
| 0 | Exit | No restore |

Read [restore mode details](docs/restore-modes.md) and [the emergency runbook](docs/recovery-runbook.md) before using options 2–8.

## Backup contents

```text
/var/lib/vz/host-config-backups/YYYY-MM-DD_HH-MM-SS/
├── backup-info.txt
├── config.db
├── etc-pve.tar.gz
├── etc-host.tar.gz
├── pve-config-backup.sh
├── system-state.txt
├── SHA256SUMS
└── VERIFIED
```

`VERIFIED` is a status marker, **not** a digital signature. `SHA256SUMS` detects unintentional changes against the included manifest, not malicious replacement of both data and manifest. The directory is highly sensitive. See [format and verification](docs/backup-format.md).

## Scheduling

The repository includes **example** [systemd service](systemd/pve-config-backup.service) and [timer](systemd/pve-config-backup.timer) files; the scheduled time is 03:30 local time with missed-run catch-up enabled. These example unit files are **not an independently validated deployment of your host's original unit**. In particular, the old chat-based installation notes used `Requires=pve-cluster.service`, which must **not** be copied blindly because the backup script intentionally stops that service. Read [systemd guidance](docs/installation.md#7-optional-systemd-scheduling) before enabling a timer.

A successful timer trigger is **not** proof of a successful backup. Running guests will cause the backup to refuse execution.

## Documentation

| Guide | Purpose |
| --- | --- |
| [Installation](docs/installation.md) | Prerequisites, local install, verification, optional scheduling, uninstall |
| [Architecture](docs/architecture.md) | Interactions among scripts, `pmxcfs`, filesystem and systemd |
| [Backup format](docs/backup-format.md) | Exact backup-set files, scopes, retention, checks |
| [Operations](docs/operations.md) | Routine backup workflow and safe verification |
| [Restore modes](docs/restore-modes.md) | Every recovery option, effects and constraints |
| [Recovery runbook](docs/recovery-runbook.md) | Preparation, emergency escalation and manual safety checks |
| [Security model](docs/security-model.md) | Sensitive material, trust boundaries, known code caveats |
| [Compatibility](docs/compatibility.md) | Platform and topology restrictions, tested vs. unproven |
| [Limitations](docs/limitations.md) | Explicit exclusions and unsupported use cases |
| [Source provenance](docs/source-provenance.md) | Frozen versions, original upload hashes, publication changes |
| [Release checklist](docs/release-checklist.md) | Publishing prerequisites and integrity-only validation |
| [GitHub publication](docs/github-publication.md) | Search-friendly repository metadata, attribution and publishing steps |

## Project and publication status

This repository distributes **unaltered script snapshots** with English documentation. It is a **documentation and packaging publication**, not a new software development or code-fix release. The publisher is [@Saugjunkie2](https://github.com/Saugjunkie2), and the selected license is [MIT](LICENSE). Previous host validation is not represented as new test execution during repository packaging.

For vulnerabilities or sensitive reports, see [SECURITY.md](SECURITY.md). No warranty or support commitment is implied by the project description.

## Credits, license and independence

Published by **[@Saugjunkie2](https://github.com/Saugjunkie2)**. The supplied source snapshots and new repository documentation are distributed under the [MIT License](LICENSE), subject to the rights of their respective copyright holders. If you redistribute or modify this project, retain the required copyright and license notice.

This project is **unofficial** and is not affiliated with, endorsed by, or maintained by Proxmox Server Solutions GmbH. Proxmox is a trademark of its respective owner.

For the recommended GitHub repository description, search topics and publication steps, see [GitHub publication](docs/github-publication.md).
