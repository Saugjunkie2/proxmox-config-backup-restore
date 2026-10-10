# Installation and setup

[← README](../README.md) · [Compatibility](compatibility.md) · [Operations](operations.md)

## 1. Decide whether this host is suitable

The backup script targets an **individual standalone node**, not a Proxmox cluster; it refuses cluster indicators and active QEMU/LXC guests. It temporarily stops `pve-cluster` to capture the underlying database, then restarts and verifies it. Do not install this assuming uninterrupted availability. Have a **working local/IPMI console** and a maintenance window.

The restore script expects backup sets created by the companion tool, bearing the current node name. It checks the backup before offering recovery. Domain-specific recovery requires further facilities such as Proxmox Perl modules, `ifupdown2`, `sshd`, Python, or `pve-firewall` depending on the selected mode.

**Avoid these situations:** clusters, guests that cannot be stopped, reliance on the same SSD as the only disaster-recovery copy, lack of local console, uncertainty over remote access, or use of an unsupported live network layout.

## 2. Prerequisites

- Proxmox VE with `pve-cluster`/`pmxcfs`, standard Proxmox CLI (`qm`, `pct`, `pvesm`, `pveum`, `pveversion`, `pvesh`, `pve-firewall`) and GNU userland utilities.
- Root privileges; Bash and `sqlite3`, `python3`, GNU `tar`, `sha256sum`, `flock`, `mountpoint`, `systemctl`, `pgrep`, `ip`, `ss`, `lsblk`, `findmnt`, `timeout`, `dpkg-query`, and an executable `/usr/sbin/sshd`. The backup performs explicit command preflight.
- Native Proxmox Perl modules and `perl` for certain restore modes; `ifquery`, `ifreload`, `unshare`, `diff`, and network tools for network restoration.
- Writable local secure backup directory with adequate free space (especially because `etc-host.tar.gz` archives host configuration), correct system clock, and no competing backup/restore maintenance.

Install explicitly missing Debian utilities according to your distribution policy. At minimum, SQLite and Python can normally be installed using:

```bash
apt-get update
apt-get install sqlite3 python3
```

These two packages alone do not guarantee every mode's prerequisites.

## 3. Obtain and verify the release

Obtain the project from the [public GitHub repository](https://github.com/Saugjunkie2/proxmox-config-backup-restore). Avoid piping downloaded shell scripts directly into a root shell. For a **fresh** checkout on a host where `git` is installed:

```bash
git clone https://github.com/Saugjunkie2/proxmox-config-backup-restore.git
cd proxmox-config-backup-restore
sha256sum -c SHA256SUMS
```

Alternatively download and extract a GitHub source archive, change into its extracted root, then run `sha256sum -c SHA256SUMS`. The `bundle-2026.10` release tag preserves the **initial publication**; `main` includes later documentation-only commits such as the extracted development histories. Both carry the frozen script bytes.

Expected result: both `scripts/` files should print `OK`. Compare hashes with a trusted release announcement; a manifest downloaded from the same untrusted location is not independent authenticity proof.

## 4. Install, without changing source bytes

```bash
# Run as root from the repository root.
install -d -o root -g root -m 0700 /var/lib/vz/host-config-backups
install -o root -g root -m 0700 scripts/pve-config-backup /usr/local/sbin/pve-config-backup
install -o root -g root -m 0700 scripts/pve-config-restore /usr/local/sbin/pve-config-restore
```

`install` sets permissions and ownership; it does **not** rewrite the script payload. To compare deployed files with the repository copies:

```bash
sha256sum scripts/pve-config-backup /usr/local/sbin/pve-config-backup
sha256sum scripts/pve-config-restore /usr/local/sbin/pve-config-restore
cmp -s scripts/pve-config-backup /usr/local/sbin/pve-config-backup && echo 'Backup bytes match'
cmp -s scripts/pve-config-restore /usr/local/sbin/pve-config-restore && echo 'Restore bytes match'
```

Do not treat a hash/syntax match as functional proof.

## 5. First manual backup

Ensure **every VM and container is shut down** before starting. From a console-capable maintenance session:

```bash
qm list
pct list
systemctl is-active pve-cluster
mountpoint /etc/pve
/usr/local/sbin/pve-config-backup
```

`qm list` and `pct list` are human-readable diagnostics, **not** replacements for the script's guest-safety checks. If the script refuses execution, do not bypass it or edit its checks. See [operations](operations.md) for post-backup verification.

## 6. Restore menu installation

Launch in a root shell:

```bash
/usr/local/sbin/pve-config-restore
```

You must already have at least one compatible `VERIFIED` backup; the program selects and verifies it. Menu option **1** is a read-only backup verification. Do **not** use options **2–8** just to verify installation; those are mutating recovery operations. Read [restore modes](restore-modes.md) first.

**SSH restore note:** the SSH transaction has its own strict expectations for the installed SSH configuration tree and the `/run/dateifreigabe/*.conf` include. Do not install the separate legacy `Dateifreigabe` Root-login helper merely to make installation “complete”. See [compatibility](compatibility.md).

## 7. Optional systemd scheduling

The supplied unit files are **examples** and were **not** run on a Proxmox host as part of this repository packaging. Review them against your actual loaded service relationships before use. They avoid `Requires=`, `BindsTo=` and `PartOf=` coupling to `pve-cluster`, because the backup intentionally stops it. The example has no fifteen-minute hard timeout over the quiescent snapshot window; operational hung-job monitoring remains an administrator responsibility.

```bash
install -o root -g root -m 0644 systemd/pve-config-backup.service /etc/systemd/system/pve-config-backup.service
install -o root -g root -m 0644 systemd/pve-config-backup.timer /etc/systemd/system/pve-config-backup.timer
systemctl daemon-reload
systemd-analyze verify /etc/systemd/system/pve-config-backup.service /etc/systemd/system/pve-config-backup.timer
systemctl cat pve-config-backup.service
systemctl show pve-config-backup.service -p Requires -p BindsTo -p PartOf -p StopPropagatedFrom
systemctl enable --now pve-config-backup.timer
systemctl list-timers --all pve-config-backup.timer
```

The timer uses `OnCalendar=*-*-* 03:30:00`, `Persistent=true`. A missed schedule can run shortly after startup. This can coincide with running or starting guests: **the backup is expected to refuse operation in that case**. Check outcomes in:

```bash
journalctl -u pve-config-backup.service -n 100 --no-pager
systemctl status pve-config-backup.service --no-pager -l
```

A completed oneshot service may display `inactive (dead)`; inspect its **result** and actual backup verification status. Do not claim a backup exists based solely on an active timer.

### Important warning about the legacy notes

An older installation transcript contained `Requires=pve-cluster.service` and `TimeoutStartSec=15min`. Those lines are **not** carried forward into this example because dependency-stop propagation and a forced service timeout require careful live-system justification for a backup that deliberately stops `pve-cluster`. The supplied example has **not** been proven safe for every deployment; administrators must review actual host units.

## 8. Stop scheduling / uninstall

To disable the optional timer without deleting backup data:

```bash
systemctl disable --now pve-config-backup.timer
```

Do not delete backup sets without separately planning secure retention/off-host copies. To uninstall the optional unit files, remove only the two files you installed and run `systemctl daemon-reload`. To remove installed commands, manually remove only `/usr/local/sbin/pve-config-backup` and `/usr/local/sbin/pve-config-restore` after verifying they are the files you intend to remove. Never recursively remove `/var/lib/vz/host-config-backups` as a shortcut.
