# Publication changelog

## Full development history (extracted from the frozen scripts)

- **[Backup development history — 1.3 through 1.6](docs/history/backup.md):** all 12 source revision headings and 30 change bullets, translated into English from the original German comments.
- **[Restore development history — 1.4 through 1.14](docs/history/restore.md):** all 22 source revision headings and 115 change bullets, with the original revision labels retained.

The histories above are reconstructed *from the immutable published script headers*. They are **not** separate historical Git commits, do **not** imply that every intermediate draft was separately released, and do **not** assert that tests mentioned by the original authors were independently repeated during GitHub packaging. The source headers are the authoritative record of their original wording.

---

## 2026-10-10 — Initial repository package

Repository: `proxmox-config-backup-restore`. Publisher: [@Saugjunkie2](https://github.com/Saugjunkie2). GitHub publication date, once completed, is recorded separately through Git commits and Releases.

- Placed unchanged `pve-config-backup` version 1.6 and `pve-config-restore` version 1.14 in `scripts/`.
- Added English documentation: architecture, installation, operations, backup format, restore modes, recovery runbook, compatibility, security and limitations.
- Added source identity hashes, publication checklist, conservative systemd examples and repository-integrity checks.
- Added the MIT License, project attribution, GitHub search metadata and a public-repository setup guide.
- **No Bash, Python or Perl implementation changes. No new Proxmox host tests performed as part of this packaging.**

See internal script headers for historical source revision notes. Those headers remain unchanged and must not be represented as new GitHub commits or reproduced test results.
