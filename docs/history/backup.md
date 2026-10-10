# pve-config-backup — complete source-header changelog

**Published script version:** `1.6`. **Historical entries:** 12; **historical change bullets:** 30.

This is an English presentation of the entire changelog block embedded in the immutable published script. It records *historical statements written in the source*, not GitHub commits or independently re-executed tests.

The backup source history was written in German. The entries below translate its substance into English; retain version labels, dates, change identifiers and ordering exactly as recorded. For the original German wording, consult the linked immutable script header. Statements about old implementations (for example online SQLite `.backup` in version 1.4) are historical and must **not** be taken as descriptions of the present 1.6 implementation.

Entries are kept in the source order, including repeated product version numbers, draft labels and older mechanisms subsequently superseded. `ENTWURF` means *draft*; its appearance is preserved from the original source. A draft label in an old header is not independently changed into a release claim.

**Primary evidence:** [`scripts/pve-config-backup`](../../scripts/pve-config-backup), source-header lines 12–102. The original comments remain byte-for-byte unchanged.

---

## 1.6-B10v2-R2 (ENTWURF, 2026-10-07)

*Original source heading: line 12.*

- R2: A pmxcfs stop is considered clean only when pve-cluster is actually inactive, reports `Result=success`, `/etc/pve` is unmounted, and no non-zombie pmxcfs process remains. A failed/timeout/SIGKILL state must never admit the quiescent snapshot window.
- An error during the actual stop transition is retried as a bounded, complete snapshot attempt, within the existing three-attempt budget, but only after fully successful pmxcfs recovery. Other masking or inspection failures remain immediately fail-closed/fatal.

## 1.6-B10v2-R1 (ENTWURF, 2026-10-07)

*Original source heading: line 21.*

- B10v2: pve-cluster is first stopped under its normally loaded unit for the quiescent snapshot window. This preserves the unit-specific `TimeoutStopSec` contract during shutdown.
- The runtime mask is applied only after a completely stopped state has been proven. A second complete stop proof while masked closes the stop-to-mask race in a fail-closed manner. The SQLite-sidecar, snapshot, and recovery gates remain functionally unchanged.

## 1.5 (RELEASE, 2026-10-04)

*Original source heading: line 29.*

- B-10 R8: ABA hardening. The already verified static contents of the human-readable `/etc/pve` archive are semantically compared with the persistent tree in the quiescent `config.db`. The `PRE==POST` comparison remains in place as an additional check.
- Deployment additionally checks the effectively loaded unit model in a fail-closed manner for `BindsTo=`/`PartOf=` dependencies on `pve-cluster.service`.

## 1.5-B10-R7 (ENTWURF, 2026-10-04)

*Original source heading: line 35.*

- B-10 R7: Guest-state parsers explicitly handle errors in `awk`/`xargs` in a fail-closed manner; they do not rely on `set -e` in `if`/`||` call contexts.
- Deployment and manual rollback additionally coordinate nonblockingly via `/run/lock/pve-config-restore.lock`. The production backup job deliberately does not acquire this restore lock.

## 1.5-B10-R6 (ENTWURF, 2026-10-04)

*Original source heading: line 41.*

- B-10 R6: The backup core remains functionally unchanged from R5. The generated manual rollback helper now also handles the state 'exact version 1.4 + its own stale R6 guard' in a fail-closed manner and does not falsely report success; guard evidence and the current timer state are retained.

## 1.5-B10-R5 (ENTWURF, 2026-10-04)

*Original source heading: line 46.*

- B-10 R5: The backup core remains functionally unchanged from R4; R5 hardens only deployment/recovery against parallel service starts, in-place writes to the live file, and persistent-timer catch-up.

## 1.5-B10-R4 (ENTWURF, 2026-10-04)

*Original source heading: line 50.*

- B-10 R4: The protected pmxcfs window remains active until successful POST-`/etc/pve` capture and a final health gate. Errors in the POST manifest or final health check force recovery.

## 1.5-B10-R3 (ENTWURF, 2026-10-04)

*Original source heading: line 54.*

- B-10 R3 fail-closed hardening: Broken Corosync symlinks are rejected as cluster indicators; pmxcfs health and the `config.db` source are revalidated immediately before the offline window.
- The runtime-mask state is rechecked immediately before applying the mask; `mktemp` becomes part of the required-command gate.
- The full-stop proof distinguishes `ps`/`mountpoint` inspection errors from 'no process' / 'not mounted'; SQLite-sidecar symlinks fail closed.

## 1.5-B10-R2 (ENTWURF, 2026-10-04)

*Original source heading: line 62.*

- B-10 R2 hardening: Guest gates handle errors from `pgrep`/`qm`/`pct` in a fail-closed manner; the standalone/Corosync gate is rechecked immediately before the pmxcfs offline window.
- The quiescent-snapshot architecture from R1 is otherwise unchanged.

## 1.5-B10-R1 (ENTWURF, 2026-10-04)

*Original source heading: line 67.*

- B-10: `config.db` is no longer backed up using an online `.backup` operation against the raw database of a running pmxcfs instance. On standalone nodes, the recovery snapshot is created from a quiescent SQLite database during a brief, runtime-masked pmxcfs stop window.
- Before and after the stop window, the non-dynamic `/etc/pve` contents are compared using canonical content manifests. If drift is detected, the entire pmxcfs snapshot is retried within a fixed bound and is never finalized as `VERIFIED`.
- R1 fails closed on standalone nodes that have running QEMU VM/LXC guests.
- If pmxcfs cannot be restarted in a healthy state after an error, the incomplete working directory is retained and the backup timer is stopped for manual recovery.

## 1.4 (ENTWURF, 2026-09-25)

*Original source heading: line 79.*

- B-01: Signal traps for INT/TERM/HUP. A signal-triggered abort was previously observed in the EXIT trap with exit code 0, leaving the incomplete working directory behind.
- B-02: Cleanup runs with `set +e` and without an ERR trap so that a failing message cannot interrupt subsequent cleanup steps.
- B-03: SQLite `.backup` with a 10-second `busy_timeout` instead of failing immediately on 'database is locked'.
- B-04: Tar archives are recreated up to three times on exit code 1. This status can result, among other causes, from files changing during reading; exit codes >= 2 remain fatal.
- B-05: Diagnostic commands in `system-state.txt` run with time limits and without inherited lock file descriptors.
- B-06: If present, the local `/usr/local/sbin/pve-config-restore` tool is also saved in the host archive. The scope is not expanded indiscriminately to all of `/usr/local/sbin` or crontabs.
- B-07: Data is synced to disk (`sync -f` before `mv`) before renaming to the final name.
- B-08: Retention hardening: the ten newest verified backups are kept regardless of age; orphaned working directories older than one day are removed.
- B-09: Version bump to 1.4 and this changelog.

## 1.3

*Original source heading: line 101.*

- Starting point for this revision; no earlier changelog exists in the supplied source.
