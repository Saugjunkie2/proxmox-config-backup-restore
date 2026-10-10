# pve-config-restore — complete source-header changelog

**Published script version:** `1.14`. **Historical entries:** 22; **historical change bullets:** 115.

This is an English presentation of the entire changelog block embedded in the immutable published script. It records *historical statements written in the source*, not GitHub commits or independently re-executed tests.

The restore source history is already almost entirely in English. Its original wording is retained except for the German G-04 version-bump sentence, which is translated. Status tags such as `DRAFT/ENTWURF` and retrospective notes (including `SUPERSEDED`) remain exactly as attributed in the source. The source file itself is not edited.

Entries are kept in the source order, including repeated product version numbers, draft labels and older mechanisms subsequently superseded. `ENTWURF` means *draft*; its appearance is preserved from the original source. A draft label in an old header is not independently changed into a release claim.

**Primary evidence:** [`scripts/pve-config-restore`](../../scripts/pve-config-restore), source-header lines 11–417. The original comments remain byte-for-byte unchanged.

---

## 1.14 (DRAFT/ENTWURF, C-29 SSH R6, 2026-10-08)

*Original source heading: line 11.*

- Bind parser-only and PREVIOUS-main temporary objects to their original device/inode and full owned descriptors; reject foreign replacement.
- Recheck ssh.service active at final commit and late rollback gates; momentary service health is not proof of HUP completion.
- Correct SSH helper filename and maintain unchanged VERSION=1.14.
- Dateifreigabe, Backup 1.6 and all non-SSH domains stay unchanged.

## 1.14 (DRAFT/ENTWURF, C-29 SSH R5, 2026-10-08)

*Original source heading: line 19.*

- Reverify LIVE PREVIOUS/TARGET and external Include after private cleanup and result-write I/O in SAME_STATE, REFUSED, and ROLLED_BACK branches.
- A detected FOREIGN/AMBIGUOUS final state is CRITICAL; never blind rollback.
- Correct RC20 wrapper claim; product version remains 1.14 (SSH R5).
- Dateifreigabe, Backup 1.6 and all non-SSH domains are unchanged.

## 1.14 (DRAFT/ENTWURF, C-29 SSH R4, 2026-10-08)

*Original source heading: line 27.*

- Recheck TARGET at late commit/after cleanup and before reporting success; detected foreign changes are CRITICAL, not falsely COMMITTED.
- Check immutable private directory mtime at cleanup admission; allow explicit stage-root and own cleanup mtime transitions only.
- SSH R4 remains DRAFT; no SSH host mutation or promotion performed.

## 1.14 (DRAFT/ENTWURF, C-29 SSH R3, 2026-10-08)

*Original source heading: line 34.*

- Cleanup now rejects missing as well as foreign entries in owned private trees; stage remainder derives solely from proven source-renames.
- Escrow and quarantine cleanup require the full ownership inventory; incomplete/ambiguous cleanup is CRITICAL, never silent COMMITTED.
- SSH R3 remains DRAFT; hostvalidation H0-H9 and promotion pending.

## 1.14 (DRAFT/ENTWURF, C-29 SSH R2, 2026-10-08)

*Original source heading: line 41.*

- Repair in-process signals immediately after Main/Drop-in renames: recover transaction state from owned filesystem descriptors, not post-syscall flags.
- Enforce secure SSH root/parent admission and atomic no-replace directory renames; preserve foreign stage/escrow objects instead of blind cleanup.
- Define an irreversible COMMIT cleanup boundary; post-commit failures are CRITICAL and never misrepresented as rollbackable. Product remains 1.14.
- R2 remains DRAFT; no Proxmox SSH host mutation or promotion performed.

## 1.14 (DRAFT/ENTWURF, C-29 SSH R1, 2026-10-08)

*Original source heading: line 50.*

- C-29 SSH introduces a domain-local two-object transaction for sshd_config and sshd_config.d, with strict staged parser admission and exact ownership.
- FOREIGN/AMBIGUOUS states fail closed; rollback is conditional, not blind. Same-state performs neither rewrites nor reload; Dateifreigabe stays out of scope.
- HUP is logged as a reload request, never a proven daemon reload completion.
- SSH R1 is a DRAFT only: independent Proxmox host validation H0-H9 pending.

## 1.13 (C-29 PASSWORDS R2 FIX, 2026-10-08)

*Original source heading: line 59.*

- A failed stat() on a newly created PASSWORDS temporary auth file now latches unproven cleanup before returning. The transaction must report the critical cleanup state (RC 78) instead of an ordinary failed write.
- No blind deletion without proven temporary-file ownership; other C-29 domains are unchanged. SCRIPT_VERSION remains 1.13.

## 1.13 (DRAFT/ENTWURF, C-29 PASSWORDS R1, 2026-10-07)

*Original source heading: line 65.*

- C-29 PASSWORDS adds a domain-local transactional restore for user.cfg and priv/shadow.cfg. Product development advances from validated 1.12 to 1.13 and PASSWORDS starts at R1; Storage/VM/C-28 behavior is otherwise retained.
- The complete authentication writer lock set is held in native Proxmox order (domains -> TFA -> user -> token -> shadow) across PREVIOUS capture, QUIESCE, TARGET apply, validation, commit or rollback. The helper pins the current /etc/pve FUSE mount and refuses cluster/corosync scope for this standalone R1.
- Reader-visible mixed auth state is fail-closed with a minimal empty user.cfg QUIESCE before password transition. TARGET shadow is made live before TARGET user.cfg; rollback keeps QUIESCE until PREVIOUS shadow is exact, then restores PREVIOUS user.cfg. FOREIGN/AMBIGUOUS pairs are never blindly overwritten.
- Strict raw schema/duplicate checks are combined with the installed native Proxmox parsers. R1 refuses API-token metadata/secrets, token ACL principals, legacy user keys, modern TFA state, custom realms and orphan shadow entries; a PVE user without a password remains valid. No token/TFA/domain merge occurs.
- Same-state TARGET/PREVIOUS commits without rewriting auth files. SIGKILL, reboot, kernel panic and power loss remain C-30 scope; existing tickets are not claimed to be revoked by a password restore.

## 1.12 (DRAFT/ENTWURF, C-29 VM R3, 2026-10-07)

*Original source heading: line 84.*

- Standalone-scope admission is hardened fail-closed for dangling Corosync symlinks: Bash checks both existence and symlink objects, and the pinned pmxcfs Perl context applies the same rule before/after live mutation.
- VM native-lock admission now reports a neutral verification failure at the caller instead of incorrectly describing every failure as a 10-second timeout. Detailed causes remain emitted by the lock helper itself.
- R2 transaction/locking architecture is otherwise unchanged; Storage, C-28, C-30 and other C-29 domains are untouched. SCRIPT_VERSION remains 1.12.

## 1.12 (DRAFT/ENTWURF, C-29 VM R2, 2026-10-07)

*Original source heading: line 93.*

- Corrective VM revision: native QEMU lock path is resolved from the installed PVE::QemuConfig implementation instead of hardcoding one path. While the Bash lock is held, a separate PVE::QemuConfig->lock_config_full() probe must be unable to enter its callback; otherwise VM restore refuses before PREVIOUS capture/live mutation. This makes lock-contract drift fail closed. /var/lock and /run/lock aliases are canonicalized before FD identity checks, so Debian's /var/lock -> /run/lock symlink is handled.
- C-29 VM product scope is explicitly standalone-only for 1.12; presence of local or pmxcfs corosync configuration refuses this VM restore path, and the pinned pmxcfs context rechecks that scope before/after live mutation. No generic cluster/cross-type serialization guarantee is claimed.
- VM operation admission now declares readlink explicitly, and QEMU-process probing distinguishes pgrep RC1 (no match) from RC>1 (probe failure), refusing fail-closed on process-detection errors.
- R1 transaction architecture is otherwise retained; Storage/C-28/C-30 and other C-29 domains are intentionally unchanged. SCRIPT_VERSION remains 1.12.

## 1.12 (DRAFT/ENTWURF, C-29 VM R1, 2026-10-07)

*Original source heading: line 110.*

- First domain-local VM revision after the completed C-29 Storage domain was promoted into the validated 1.11 product baseline.
- C-29 VM-domain integration: VM restore now uses the shared transaction state machine with immutable TARGET, stable PREVIOUS, exact ownership gates, exact TARGET-before-commit and PREVIOUS-after-rollback checks.
- The native Proxmox per-VM flock remains held across PREVIOUS capture, live mutation, validation, commit or rollback. Live VM-file transitions additionally pin the current /etc/pve FUSE mount so pmxcfs loss cannot redirect a write into an underlying local /etc/pve directory.
- Staged VM TARGETs are strict-parsed before mutation with PVE::QemuServer::parse_vm_config(..., 1). Post-write/rollback validation is non-interactive and fails closed on qm diagnostics. Unexpected in- process exits use the same ownership-aware rollback hook as Storage.
- Product target version is 1.12. C-29 domain revisions are local to their domain, so VM development starts again at R1. No C-30 behavior is added.

## 1.11 (DRAFT/ENTWURF, C-29 R7, 2026-10-06)

*Original source heading: line 126.*

- A critical session end latches recovery-required for this process. Further restore menu actions and session admission are blocked; backup inspection and exit remain available. Transaction reset does not clear this latch.
- Keep the backup timer stopped and retain remaining workdir evidence even if moving it to the emergency directory failed. No in-process override.
- Shared post-critical lifecycle policy changes for all restore domains; domain restore bodies and C-28 network logic remain unchanged. Validator deadlines still require host evidence. SCRIPT_VERSION remains 1.11; no C-30.

## 1.11 (DRAFT/ENTWURF, C-29 R6, 2026-10-06)

*Original source heading: line 135.*

- Identical admitted Storage PREVIOUS/TARGET descriptors use a locked no-op instead of rewriting storage.cfg. TARGET validation and the final locked commit gate remain mandatory. This avoids same-byte writes being hidden by PREVIOUS reclassification after a late callback failure. SCRIPT_VERSION remains 1.11. No C-28 or C-30 behavior is changed.

## 1.11 (DRAFT/ENTWURF, C-29 R5, 2026-10-06)

*Original source heading: line 141.*

- Storage temporary-file cleanup is a required postcondition, including in-process exceptions after temporary-file creation. Cleanup only removes an object matching the identity of this operation's own temporary file.
- Unproven cleanup remains AMBIGUOUS/CRITICAL through apply, rollback and EXIT handling; exact storage.cfg bytes cannot hide an unresolved artifact. SCRIPT_VERSION remains 1.11. No C-28 or C-30 behavior is changed.

## 1.11 (DRAFT/ENTWURF, C-29 R4, 2026-10-06)

*Original source heading: line 148.*

- R4 closes the Storage-pilot apply and rollback compare/write races by executing the critical storage.cfg compare/write transition inside PVE::Storage::lock_storage_config(), the same Proxmox cfs lock protocol used by normal storage configuration writers.
- Storage live-state gates now verify pmxcfs IPC plus the current /etc/pve FUSE mount and pin that mount through an open directory descriptor during locked writes, preventing fallback writes into an unmounted local path.
- `pvesm status` remains outside the cfs lock. Its R4 contract is deliberately limited to command acceptance (RC 0 and no stderr diagnostics); it does not claim that every configured backend is reachable/active. Final TARGET and PREVIOUS identity gates are reacquired under the Proxmox storage lock.
- Lock/callback failures are classified by locked postconditions before the transaction decides whether a live mutation occurred; a late cfs-lock callback failure therefore cannot be mislabeled as "no mutation".
- R3 TARGET/PREVIOUS prevalidation, explicit transaction states, no-write PREVIOUS rollback, phase-neutral runtime naming and C-28 isolation remain. SCRIPT_VERSION remains 1.11; R4 is a corrective revision, not 1.12.

## 1.10 (DRAFT/ENTWURF, 2026-10-03)

*Original source heading: line 166.*

- C-28 R6: the offline health fixture no longer assumes that a host has an interface named eth0. Host-validation H1 on localserver001 exposed that portability bug because the real physical port is LAN1. The product connectivity/runtime logic is unchanged from R5.
- C-28 R5: package/runtime naming cleanup completes the phase-neutral naming rule for current technical artifacts. The offline harness uses a generic temporary path, and stale R1/R2 revision labels in current comments were neutralized. Connectivity/runtime behavior is unchanged from R4.
- C-28 R4: phase-number coupling was removed from runtime identifiers. Connectivity globals, local variables, WORKDIR artifact names and Python heredoc markers now use generic network/connectivity/health names. Stale runtime messages that named the R2 revision were neutralized to the connectivity-watchdog scope. Runtime logic is otherwise unchanged from R3.
- C-28 R3: RC0 plus error/Traceback diagnostics now follows the same automatic PREVIOUS rollback/re-apply/health-confirm path as a non-zero TARGET ifreload. The final successful health pass rechecks restored files, ifupdown2 runtime state and the monotonic deadline before commit. PREVIOUS is always re-applied during recovery even when its C-28 health profile is unavailable; lack of a profile still prevents a false success claim and enters the critical/manual-recovery path.
- C-28 R2: a live network apply is provisional until a locally derived management profile passes carrier/interface/address/route/MTU and bound gateway probes three consecutive times inside a 10-second deadline. PREVIOUS is captured from the known runtime before live writes and is health-confirmed after automatic rollback. Unsupported/ambiguous topology blocks only live apply and keeps the controlled-reboot restore path. C-28 uses no external service, daemon or persistent crash journal.
- C-28 connectivity helper functions use the generic network_connectivity_* namespace instead of embedding the project phase number in function names.
- Version bump to 1.10.

## 1.9 (DRAFT/ENTWURF, 2026-10-01)

*Original source heading: line 197.*

- Post-C-26 review: restore_ssh now checks every live SSH write and every rollback write explicitly. A partial cp/rm failure enters a controlled rollback path instead of letting global `set -e` terminate the tool while the restore session is active.
- SSH rollback failure is reported as CRITICAL and keeps the automatic backup timer stopped for manual recovery.
- Version bump to 1.9.
- C-27 R7: before bridge MTUs are neutralized, a first private syntax pass keeps those MTUs intact (while retaining the C-26 fd rewrite). Only the exact reviewed bridge-MTU false-positive may be ignored there, so dependent checks such as VLAN MTU > bridge-parent MTU keep their source information. Pass A records exactly which pre-approved bridges actually emitted that false-positive; pass B neutralizes MTU only for that seen subset, not for every raw allowlisted bridge. Hard validator reasons in both syntax passes (busy, lock-unavailable, isolation) are preserved before generic diagnostic classification, and observed ifupdown2 concurrency is retained across both syntax passes. The real/restored candidate stays unchanged. Ambiguous, duplicate or invalid MTU input fails closed. The workaround is pinned to the exact reviewed ifupdown2 package version plus the C-27-relevant code/configuration files. C-27 does not introduce another SCRIPT_VERSION bump; it extends 1.9.

## 1.8 (DRAFT/ENTWURF, 2026-09-30)

*Original source heading: line 219.*

- C-26 REVIEW3: Network restore validation handles the ifupdown2 3.3.0 `bridge-fd 0` syntax-check regression without changing backed-up values. A frozen candidate tree is captured before validation, and every parser or syntax check uses that same candidate through a private copy. Only unambiguous bridge-fd 0 + STP off/no/default-off stanzas are changed to fd 2 inside the throw-away copy. /etc/network itself must be a real directory, copied validation paths are canonicalized before rewriting, and the validation namespace gets a private /var/log so synthetic fd-2 copies are not added to the host's persistent ifupdown2 debug history.
- Network validation no longer offers generic KEEP for ifupdown2 parser diagnostics. ifquery stderr, error/traceback output and unknown syntax messages fail closed. The only rc=0 syntax warning accepted automatically is "interface not recognized" for an interface that has its own stanza describing a software-created interface. Required lower links are derived from the complete parsed candidate, including non-auto VLAN dependencies. Existing software interfaces are not subjected to physical .link pinning; hardware-backed links must exist at runtime or be reproducible by staged .link pinning matched to a detected NIC's persistent hardware MAC.
- The current live configuration is checked only as an advisory before the restore session starts. A diagnostic can be overridden explicitly with CONTINUE so a broken/missing live interfaces file does not prevent recovery from a good backup. Isolation failures and an unresolved ifupdown2 lock remain hard refusals. No pre-restore snapshot/retention run is triggered merely by an advisory refusal.
- [SUPERSEDED later in this 1.8 draft] An earlier REVIEW3 replacement path removed the destination before cp -a to avoid writes through destination symlinks. The later sibling-staging/mv implementation below replaces that mechanism. Snapshot existence still records files, directories and dangling symlinks, and rollback equality checks file type/symlink target and content.
- The Proxmox network configuration lock (/etc/network/.pve-interfaces.lock) is held from restore-session setup through validation/apply/rollback. The candidate /etc/network tree is captured BEFORE validation; both ifquery and the private ifreload syntax check read that captured tree, and the live tree must still match it before apply or successful no-apply completion. Parser sources outside /etc/network/interfaces.d and symlinks inside interfaces.d are refused so the validated parser input cannot change behind the snapshot.
- ifupdown2 rc 89 is retried for a bounded 30-second window with increasing delays and reported separately from validation and mount-namespace errors. Live apply remains strict. After a failed live apply, rollback still requires byte/type-identical old files plus a successful strict re-apply.
- If a target bridge is STP-off/no/default-off with a forward delay outside the kernel STP range 2..30 while the existing runtime bridge is not unambiguously stp_state=0, live apply is not offered. This avoids one-step ERANGE transitions; the restored files are left for a controlled reboot instead. At the 1.8 stage, Bridge-MTU remained a separate C-27 limitation; it is addressed later in the 1.9 C-27 section.
- The post-write validation window also fingerprints ifupdown2's configured ifstatenew file. state_dir is read with RawConfigParser-compatible semantics; daemon mode is refused because ifupdown2d would run outside the validation mount namespace. A foreign live apply that finishes between our lock probes or while the user is at the apply/no-apply prompt is therefore treated like concurrency: old files are restored and strictly re-applied instead of claiming that runtime state was untouched.
- Network archive structure is preflighted from the protected backup before the pre-restore snapshot. Symlinks/hardlinks/special objects in restored interfaces.d or pinning trees are refused, and staged external source paths are not accepted by C-26 validation.
- Network object replacement now copies to a private sibling first and uses no-clobber live-path transitions plus postcondition checks. The source is checked before changing the destination, mountpoints are refused, and detected concurrent live objects are preserved before rollback instead of being silently overwritten or discarded.

## 1.7 (DRAFT/ENTWURF, 2026-09-26)

*Original source heading: line 282.*

- C-01: The backup service check uses ActiveState/SubState. A running Type=oneshot service is "activating", which `systemctl is-active` reports as not active, so the previous check never detected a running backup. The timer flag is only set after the timer was really stopped.
- C-02: Before config.db is touched, pve-cluster is stopped unconditionally under the runtime mask. "Stopped" now means ActiveState inactive/failed, no /etc/pve mount and no running pmxcfs process. Previously a unit in "activating", "deactivating" or auto-restart was treated as stopped without a stop call. The mask itself is verified (is-enabled must report masked-runtime).
- C-03: The start rate limit is reset (systemctl reset-failed) before an intentional pve-cluster start. A replacement database that makes pmxcfs exit uses up the limit through Restart=on-failure, and systemd then also refused the start of the rollback database.
- C-04: Firewall reload/restart is invoked explicitly through systemd (systemctl reload-or-restart pve-firewall), and pvesm status is run with the restore/backup lock descriptors closed. On current PVE, the normal `pve-firewall restart` CLI already delegates to systemd when invoked outside PID 1; this change therefore makes that behavior explicit and keeps descriptor handling defensive instead of fixing a direct-daemon inheritance bug.
- C-05: Required backup members (sshd_config, interfaces, storage.cfg, user.cfg, priv/shadow.cfg) are checked before the restore session starts, as R-06 does for the firewall. A missing member is a normal refusal instead of an "unexpected failure" with the timer left stopped.
- C-06: Write failures in pmxcfs restores (firewall, storage, users, VM) lead into the rollback path instead of exiting with a partially applied state. Rollback steps are checked individually, and only files that were actually written are rolled back. restore_pmxcfs_from_stage is no longer used and was removed.
- C-07: archive_has_prefix fails closed when the archive cannot be listed. A listing error was read as "not present", and the live sshd_config.d, interfaces.d or pinning directory was deleted without replacement.
- C-08: sqlite3 runs with -batch -init /dev/null. A ~/.sqliterc of root (for example ".headers on") changed the output, and every integrity check failed.
- C-09: Menu numbers accept only 1 to 5 digits without a leading zero. "08" terminated the tool in the VM selection, "010" was read as octal.
- C-10: Failures of begin_restore_session and cancelled confirmations wait for Enter. The menu cleared those messages immediately before.
- C-11: Informational output after a completed restore (pve-firewall status, pvesm status, pveum, qm config) can no longer turn a completed restore into an "unexpected failure".
- C-12: VM restore refuses a live configuration with a "lock:" entry, warns when the backup configuration contains one, and holds the Proxmox per-VM lock (/var/lock/qemu-server/lock-<vmid>.conf) while writing and validating. After the lock is acquired, VMID conflicts and the running state are checked again so a qm start/migration that won the lock race cannot finish first and leave a running/moved VM to be overwritten.
- C-13: Messages of the validation commands are shown and must be accepted with KEEP, otherwise the restore is rolled back. The Proxmox parsers usually only warn about invalid lines.
- C-14: When the original pmxcfs cannot be restarted, the tool reports the pve-cluster state, a remaining runtime mask and the commands to continue.
- C-15: Session start and end, critical states and unexpected exits are written to the journal (logger -t pve-config-restore). An ERR trap reports line and command of an unexpected failure.
- C-16: A failed verification from menu 1 or 10 returns to the menu; menu 10 keeps the previous selection.
- C-17: Leftover protected copies (.restore-selected.*) are reported at startup.
- C-18: The protected copy is restricted to mode 0700 after copying; cp -a had applied the mode of the source directory.
- C-19: /usr/local/lib/systemd and /var/lock/qemu-server are created with mode 0755 if missing, instead of 0700 from umask 077.
- C-20: The exit message no longer claims "No changes made".
- C-21: Formatting: restored line continuation in restore_vm_config and comment order in restore_config_db.
- C-22: Version bump to 1.7 and this changelog.
- C-23: Commands newly used by the 1.7 safety helpers (install and sed) are part of the startup prerequisite check instead of being assumed.
- C-24: While the KEEP question after a VM restore is open, the Proxmox per-VM lock stays held (it has to, see C-12). The question now says so: qm start, backups and other Proxmox operations on this VM fail with a lock timeout (usually after 10 seconds) until it is answered.
- C-25: Review note on C-04: "outside PID 1" means that the parent process of the pve-firewall CLI is not PID 1, i.e. the CLI was not started by systemd (getppid() != 1 in PVE::Daemon). The first draft of C-04 described a direct-daemon inheritance bug; that description was wrong and was replaced in review round 2.

## 1.6 (DRAFT/ENTWURF, 2026-09-26)

*Original source heading: line 365.*

- G-01: Backup config.db verification no longer depends on SQLite URI filename support. The database is copied to a private temporary directory and checked there with a normal sqlite3 open, so the selected backup remains untouched even if SQLite creates WAL/SHM helper files.
- G-02: extract_tree and the VM-config archive listing capture tar pipeline failures explicitly instead of losing them through Bash process substitution.
- G-03: Emergency config.db recovery now refuses to replace the database if SQLite journal/WAL/SHM sidecars still exist after the clean, runtime-masked pve-cluster stop. This keeps the original database plus sidecars intact instead of relying on a later sidecar reconstruction during rollback.
- G-04: Version bump to 1.6 and this changelog.

## 1.5 (DRAFT/ENTWURF, 2026-09-25)

*Original source heading: line 378.*

- R-01: pin_selected_backup checks every step explicitly. It runs inside `begin_restore_session || return 0`, where Bash ignores `set -e`. A failing mktemp previously turned the copy into `cp -a -- "${BACKUP}/." "/"` (files in / and / set to mode 0700).
- R-02: verify_backup_path fails explicitly on SHA-256 and tar errors (previously ignored in the same context) and opens config.db explicitly read-only with immutable=1 so verification no longer changes mtime.
- R-03: cleanup runs with `set +e`, so a failing message cannot abort the remaining cleanup steps.
- R-04: HUP trap and signal-protected critical sections. An SSH disconnect during an active restore previously restarted the backup timer and deleted the rollback copies.
- R-05: Emergency config.db restore restarts the original pmxcfs if a step fails before replacement. A failed sync after replacement or rollback is critical and prevents automatic activation/reboot. Before a rollback, pve-cluster must be verifiably stopped. If no config.db existed before, pve-cluster stays stopped instead of starting with an empty configuration. A missing rollback copy is reported as such. If SQLite sidecars from an unclean shutdown were already removed when a later step fails, the original database is not restarted automatically. While the live database is offline, pve-cluster is runtime-masked so another systemd activation cannot reopen pmxcfs during replacement/rollback; its state is also re-checked before the live database is touched. Every fail-closed path warns against reboot/start until manual verification.
- R-06: Firewall restore checks for optional host.fw/cluster.fw before the restore session starts and no longer exits the tool mid-session.
- R-07: Files in /etc/pve are written via temporary file and rename.
- R-08: Emergency config.db restore is refused when either the local corosync or the pmxcfs cluster configuration is present. The local file is checked first, so pmxcfs is only accessed when it is absent.
- R-09: Timer messages state that an enabled timer restarts on reboot.
- R-10: Version bump to 1.5 and this changelog.
- R-11: The pre-restore backup run and `ifreload -a` do not inherit the lock file descriptors (long-running helpers could keep the locks).
- R-12: extract_tree uses --no-recursion. Previously every directory with at least one file (sshd_config.d, interfaces.d, pinning .link files) made tar exit with status 2 ("Not found in archive").

## 1.4

*Original source heading: line 416.*

- Baseline of this revision (no changelog before).
