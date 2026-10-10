# Security model, trust boundaries, and known source-level caveats

[← README](../README.md) · [Security reporting](../SECURITY.md)

## Protected assets

Backups may contain `/etc/shadow`, SSH server configuration and key material, Proxmox signing/private keys, access-control definitions, storage credentials and sensitive system inventory. The tool uses restrictive root-owned directories/files and backup integrity checks, but **does not encrypt** the archives. Publishing a real backup or diagnostic set can expose credentials and infrastructure detail.

## Trust assumptions

- The host's **root account**, `systemd`, `pmxcfs`, Proxmox command-line programs, and trusted system binaries are not already compromised.
- The selected backup directory is locally protected; its hashes alone do not establish authenticity against a privileged attacker.
- A verified backup was created for the **same node**; no arbitrary cross-node migration is claimed.
- Console/IPMI remains available when access-control or networking changes become unreachable through SSH.
- Runtime locks, temporary files and systemd masks protect particular code paths; they are not guarantees of survival against power loss or kernel crashes.

## Main protections present in the code

- Preflight rejection of clusters and active guests during backup.
- Backup exclusion lock, private work directories, quiescent `config.db` snapshot, SQLite/tar verification, SHA-256 and `VERIFIED` marker.
- Restore backup verification, node matching, file staging, selected native PVE locks, concurrency/identity comparisons, conditional rollback, critical-state latch, timer coordination, protected temporary backup copies.
- SSH transaction validation in an embedded Python helper; network isolation and controlled health checks under supported conditions.

These are **source-level design features**, not proof that every error or crash path is safe in every environment.

## Known source-level caveats

The following items were identified through source inspection / isolated logic examination during packaging preparation. **They were not reproduced as destructive failures on a live Proxmox host in this packaging stage.** The source has been frozen by the maintainer; this documentation does not apply corrective patches.

| ID | Area | Caveat | Evidence classification |
| --- | --- | --- | --- |
| S-01 | Full `config.db` recovery | `pve_cluster_fully_stopped()` in restore accepts `ActiveState=failed` and does not require `Result=success`; backup 1.6 uses a stricter stop proof. Errors from some process/mount inspections can also be ambiguous. | Confirmed source difference; isolated negative-path analysis |
| S-02 | Full `config.db` recovery | Guest-query pipelines contain error-swallowing patterns, potentially interpreting `qm`/`pct`/parser failures as no running guests. | Confirmed source behavior; target-system impact unverified |
| S-03 | Standalone admission in emergency recovery | Checking Corosync configuration with `-e` does not detect every dangling-symlink situation, unlike `-e || -L`. | Confirmed shell/source difference; target-system impact unverified |
| S-04 | Legacy systemd instructions | An older example coupled the backup service with `Requires=pve-cluster.service` even though the script intentionally stops `pve-cluster`. | Documentation/deployment concern, not proof of failure with this repo's new example |
| S-05 | Firewall backend | The restore path uses `pve-firewall` and must not be described as tested for all alternative firewall services/backends. | Compatibility boundary; separate host proof unavailable |
| S-06 | Archive entries | A generic archive-name check does not, by itself, inspect every symlink target; domain-specific gates exist, but universal hostile-archive extraction safety has not been established. | Incomplete proof; **not** a demonstrated path traversal exploit |

**Implication:** Administrators must not assume that emergency database restore can autonomously verify all clean-stop and guest-state failure modes. Prefer targeted restoration when appropriate, a maintenance window, and local console. These caveats are not represented as patched/resolved.

## Crash, reboot and external-process limits

- Per-process rollback cannot reliably recover from arbitrary `SIGKILL` or sudden loss of power.
- `pmxcfs` recovery and backup-timer state must be inspected manually after a CRITICAL error.
- An enabled persistent timer can run after a reboot even if the script previously stopped it. Confirm scheduling state during recovery.
- A successful checksum or `bash -n` is **not** a full host functional test.

## Publication hygiene

- **Never commit:** a backup directory, `config.db`, `/etc/pve`, `.tar.gz` configuration archives, real `system-state.txt`, SSH private keys, password hashes, logs containing credentials or screenshots of secrets.
- Scrub example hostnames, addresses, identities and asset identifiers from issue reports and screenshots.
- Treat all real-world diagnostic output as confidential and use private reporting channels for sensitive findings.
- Preserve copyright and licensing provenance. This repository includes an [MIT License](../LICENSE), which requires retention of its license and copyright notice; it does not override any independently held third-party rights.
