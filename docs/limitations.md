# Limitations and non-goals

[← README](../README.md)

The utilities are intentionally narrower than a complete Proxmox disaster recovery solution.

1. **Not a VM or LXC backup solution.** Neither VM disks nor container root filesystems are included. Use appropriate VM/CT backup tooling separately.
2. **Not a cluster backup solution.** Backup 1.6 refuses clusters. Selected restore domains also refuse cluster use; other modes must not be generalized to safe cluster use.
3. **Not a no-downtime scheduler.** Backup requires all guests stopped and intentionally interrupts `pve-cluster` briefly.
4. **Not off-host disaster recovery.** The default local backup directory may share a disk with the operating system. SSD failure or host theft can destroy originals and backups simultaneously.
5. **Not an encrypted backup.** Sensitive secrets and diagnostic information require separate access controls and secure transport/storage.
6. **Not a universal cross-node migration.** The restore verifies node identity and expects the matching backup format.
7. **Not a universal SSH/network/firewall compatibility layer.** Domain checks may refuse valid but unsupported configurations.
8. **Not a full crash-consistent transaction log.** Protection against all power loss, kernel panic and `SIGKILL` cases has not been established.
9. **Not a guarantee of rollback.** Recovery may enter an uncertain or critical state and require manual intervention.
10. **Not a complete security certification.** Source-level caveats are documented; previous host tests are not independently reproduced in this documentation package.
11. **Not a promise that a timer trigger created a backup.** Guest activity and other preflight failures can reject scheduled runs.
12. **MIT does not verify ownership of third-party material.** The repository contains a [MIT License](../LICENSE), but the publisher must confirm rights to license every included component.
