# Restore modes and actual scope

[← README](../README.md) · [Recovery runbook](recovery-runbook.md) · [Compatibility](compatibility.md)

## Common admission and safety model

Run the restore tool in a root shell with a verified backup for **the same node**. At startup it selects a `VERIFIED` set, validates manifest and backup identity, and opens an interactive menu. A mutating operation typically stops the backup timer, pins the selected backup, attempts a **new pre-restore backup**, then holds relevant locks during mutation. If that protective snapshot cannot run, some modes offer an explicit `CONTINUE` override. **Do not use such an override without understanding why safety preconditions failed.**

A CRITICAL/uncertain transaction can latch a **manual-recovery-required** state for the process, preventing subsequent menu restores. Backup verification and exiting remain possible. A stopped timer is not durable across a reboot if still enabled.

## Option 1 — Verify selected backup

Checks the selected set, including `VERIFIED`, `SHA256SUMS`, node identity, archive readability and SQLite integrity. This is a **read-only** backup operation, not a restore. It cannot prove every recovery path will work.

## Option 2 — SSH configuration

Handles `/etc/ssh/sshd_config` and `/etc/ssh/sshd_config.d` using an embedded Python transaction helper. Performs strict ownership, object type, include, content and service checks. The tool states that the temporary `/run/dateifreigabe` tree is **not restored or changed**. Keep the existing SSH session open and use console/IPMI. A reconnect is not automatically proven solely by successful `sshd -t` or service reload.

**Compatibility warning:** the helper expects a specific include model (including `/run/dateifreigabe/*.conf`); its admission checks may reject other otherwise-valid SSH layouts. This is **not** an instruction to enable direct SSH root login.

## Option 3 — Network configuration

Covers `/etc/network/interfaces`, `interfaces.d`, and optional local systemd interface-pinning files. `/etc/network/interfaces.new` is intentionally **not** restored. Uses isolated parsing and syntax checks and staging/rollback. Live apply is limited to a supported management profile; other cases can be restricted to a controlled-reboot path. In a supported live profile, the connection watchdog checks interface/carrier/address/route/MTU and bound gateway probes within a finite window.

The validator contains a narrowly version- and fingerprint-bound `ifupdown2` workaround. Never assume it covers other releases. **If remote connectivity is lost, console/IPMI may be the only recovery channel.**

## Option 4 — Proxmox firewall

Offers node firewall only, datacenter firewall only, or both, using `host.fw` and/or `cluster.fw` when present in the selected set. Missing optional files are reported, not invented. Applying firewall configuration can immediately block SSH or the Proxmox GUI. The code uses `pve-firewall` tooling, so do not assume identical runtime handling on hosts using a different firewall backend; investigate backend-specific behavior before use.

## Option 5 — Storage configuration

Restores `/etc/pve/storage.cfg` through a staged and native-lock-aware transaction, with Proxmox storage parsing and command-level live acceptance. **This does not restore volumes, disks, storage pools, or VM data.** A successful `pvesm status` acceptance is not proof that every remote or physical storage backend is healthy.

## Option 6 — PVE users, ACLs and PVE passwords

Restores `/etc/pve/user.cfg` together with `/etc/pve/priv/shadow.cfg` as a restricted transaction. This covers supported Proxmox user/group/role/pool/ACL metadata and PVE-realm password hashes. It **does not** restore Linux `/etc/passwd` or `/etc/shadow`, API token secrets, modern TFA state, custom authentication realms or authentication ticket keys. Some complex backup/live states are explicitly refused rather than merged.

Use the console; authorization and remote logins may change immediately.

## Option 7 — One VM configuration

Selects exactly one QEMU VM configuration from the backup. Intended for **standalone** nodes; the code performs guest/runtime and VMID conflict guards and uses a native Proxmox VM configuration lock contract. This restores VM configuration metadata **not disk images**; a restored configuration may refer to storage that no longer exists.

## Option 8 — Emergency complete `config.db`

**Highest risk; emergency only.** Replaces `/var/lib/pve-cluster/config.db` from the selected verified set, after additional standalone/guest checks and a `pve-cluster` stop. It preserves emergency copies when possible and attempts to restart `pmxcfs`, validate the mount and **reboot automatically**.

This reverts **all** configuration stored in `pmxcfs` to the selected snapshot, including VM inventory and access/firewall/storage metadata. VM disk content is unaffected, potentially creating discrepancies. A rollback may fail and require manual recovery. Known source-level caveats regarding the clean-stop proof and guest command failure handling are documented in [the security model](security-model.md#known-source-level-caveats); do not treat those checks as infallible.

## Options 9, 10 and 0

Show backup metadata, select/verify another backup, or exit. These are not restore operations.
