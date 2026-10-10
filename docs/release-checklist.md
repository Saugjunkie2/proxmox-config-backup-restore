# Public publication checklist

[← README](../README.md) · [Source provenance](source-provenance.md)

**Historical pre-publication worksheet (2026-10-10):** The repository and initial GitHub Release are now public. The unchecked items below are retained as the **original pending/undocumented approvals**; do not interpret them as newly completed or silently check them. In particular, copyright and third-party licensing rights remain for the publisher to verify independently. The completed GitHub integrity workflow verifies script bytes, not Proxmox runtime behavior.

This checklist originally guided a **documentation-and-packaging release**, not a new functional software release or host security audit.

## Mandatory ownership and legal decisions

- [x] Select the intended GitHub owner (`Saugjunkie2`) and repository slug (`proxmox-config-backup-restore`) for this prepared package. The repository itself has **not** been created by this step.
- [ ] Independently confirm that the publisher has the rights to license both scripts, their embedded helpers and documentation under MIT, including any incorporated third-party material.
- [x] Add the selected [MIT License](../LICENSE) with copyright line `Copyright (c) 2026 Saugjunkie2` (using the public GitHub username).
- [ ] Confirm the public copyright notice matches the publisher's desired ownership attribution before pushing.

## Content protection

- [ ] Verify both source hashes against a trusted original; do not change script bytes.
- [ ] Search proposed tracked files for secrets, private IPs, usernames, real backups, private hostnames and API credentials. The frozen Restore source retains the historical comment-only hostname `localserver001`; approve this disclosure consciously rather than silently editing frozen source.
- [ ] Confirm `git status` includes **only** documentation, sample units, workflow, metadata and the two frozen scripts.
- [ ] Ensure no `config.db`, actual `etc-*.tar.gz`, real `system-state.txt` or host logs were accidentally staged.
- [ ] Keep original raw source files and private audit evidence outside the public repository.

## Documentation and packaging

- [ ] Check links, English prose, internal version numbers and exact filenames.
- [ ] Confirm installation examples do not blindly reproduce obsolete `Requires=pve-cluster.service` and the old temporary root SSH helper.
- [ ] Keep limitations and known unresolved safety caveats prominent.
- [ ] Review optional systemd units on the *actual host* **only if separately authorized**.
- [ ] Keep claims of host testing separate from repository integrity checking.
- [ ] Review license compatibility of all embedded third-party elements and any copied text before publishing.

## Suggested git publication sequence (after approval)

```bash
# From this prepared repository directory (not run automatically):
git init -b main
git add README.md CHANGELOG.md CONTRIBUTING.md SECURITY.md LICENSE docs systemd scripts SHA256SUMS .gitignore .gitattributes .editorconfig .github
git status --short
git diff --cached --stat
git commit -m "Publish frozen Proxmox host configuration tools and English documentation"
# Set remote and push only after reviewing the final contents and legal rights.
# Suggested remote: https://github.com/Saugjunkie2/proxmox-config-backup-restore.git
```

**Do not publish as "security audited", "validated on all PVE versions" or "production-safe for every host" without the corresponding evidence.**
