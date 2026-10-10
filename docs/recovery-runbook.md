# Operational recovery runbook

[← README](../README.md) · [Restore modes](restore-modes.md) · [Security](security-model.md)

## Before any restore

1. Identify the incident and the **smallest** configuration scope that needs recovery; do not use full `config.db` replacement for a single firewall rule.
2. Have **working local/IPMI console**, current system details, admin credentials and an offline/off-host copy of irreplaceable recovery material.
3. Record the existing service state (`pve-cluster`, SSH, network, firewall, backup timer) privately. Do not post raw state logs or archives publicly.
4. Understand whether guests, cluster services or storage backends are in use. Some restore modes refuse unsupported conditions; do not bypass their checks.
5. Select the intended backup and use menu **1** to verify it; review the timestamp and node identity.
6. Read the operation-specific description; options **2–8** can modify live configuration.

## Choose the narrowest mode

| Incident | Preferred first consideration | Avoid confusing with |
| --- | --- | --- |
| SSH config damage | Option 2, console/IPMI available | Replacing whole `config.db` |
| Host network config damage | Option 3, restricted validation/live-apply rules | Assuming a remote SSH session survives |
| PVE firewall mistake | Option 4 | Assuming every backend activates identically |
| `storage.cfg` mistake | Option 5 | Restoring destroyed storage data |
| PVE users/ACL/PVE-realm password issues | Option 6, with scope restrictions | Linux account password repair |
| One VM's configuration missing | Option 7 | Restoring the VM disk |
| Severe standalone `pmxcfs` database damage | Option 8 only with independent safety review | A routine rollback |

## If a step enters CRITICAL/manual-recovery status

1. **Stop issuing additional restore operations.** The tool may preserve workdir evidence and block further mutations.
2. **Do not reboot or start `pve-cluster` blindly** if database durability/replacement status is uncertain. Follow the tool's explicit warning, not a generic recipe.
3. Inspect whether `pve-cluster` is active, `/etc/pve` is genuinely mounted, any runtime mask is set, and whether original/emergency `config.db` copies remain. Use console/IPMI.
4. Do not clear protected evidence or re-enable the automatic timer until recovery is verified. A persistent enabled timer can reactivate after reboot.
5. If recovery exceeds your administrative experience, obtain qualified Proxmox assistance with sanitized diagnostics; avoid posting private backups.

## Full database restore is last resort

A full `config.db` replacement affects all stored `pmxcfs` configuration, not merely the visibly broken component. VMs created after the backup may lose configuration entries while their disks remain; access policies and storage inventory can revert. The script attempts a reboot after successful activation. **Known clean-stop/guest-query limitations are documented** in [the security model](security-model.md#known-source-level-caveats).

## After any successful targeted restore

Use console/SSH/GUI appropriate to the domain to inspect: effective SSH login, interface/routes, effective firewall rules, storage connectivity, PVE user authorization, and VM configuration consistency. A validator's command acceptance does not guarantee the entire real-world service is usable. Preserve an incident note privately; never upload an actual backup to the repository.
