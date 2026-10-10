# Publishing and discovery on GitHub

[← README](../README.md) · [Release checklist](release-checklist.md)

**Historical first-publication guide:** This repository is **already public**, and its first bundle was released as [`bundle-2026.10`](https://github.com/Saugjunkie2/proxmox-config-backup-restore/releases/tag/bundle-2026.10). The steps below describe the original setup process; **do not run `git init`, add `origin`, or create the repository again inside an existing checkout**. For documentation updates, use the existing Git checkout and normal commit/push workflow.

This guide records repository **metadata and original publication guidance**. It does not run the scripts or modify frozen source files.

## Recommended repository identity

| Field | Copy-ready value |
| --- | --- |
| Owner | `Saugjunkie2` |
| Repository name | `proxmox-config-backup-restore` |
| Display title (README) | `Proxmox Config Backup & Restore` |
| GitHub description | `Proxmox VE host configuration backup and restore scripts for standalone nodes. Verified pmxcfs snapshots, selective recovery, and safety checks.` |
| Visibility | Public, when the owner approves publication |
| License | MIT (`LICENSE`) |
| Primary language | Shell (Bash), with embedded Python/Perl and English Markdown documentation |

**Suggested GitHub Topics** (copy individually into the GitHub “About” dialog):

`proxmox`, `proxmox-ve`, `pve`, `backup`, `restore`, `config-backup`, `configuration-backup`, `linux`, `bash`, `sysadmin`, `pmxcfs`, `proxmox-restore`

These topics describe what the project actually covers; they do **not** claim general VM image backups or Proxmox Backup Server support. GitHub search ranking and search-engine indexing cannot be guaranteed.

## Why this name

The repository name contains the searched technology (**proxmox**) and two core actions (**backup** and **restore**), with **config** narrowing the scope to host configuration. It is shorter and more memorable than `proxmox-host-config-backup-restore` and avoids a marketing brand users would not know to search for.

The README heading and its first paragraph use the full name **Proxmox VE** and explain that the repository contains a **host configuration backup and restore** solution for standalone nodes. The description and topics reinforce the same intent without repeating unrelated keywords.

## Attribution and reputation

- Publish under the intended personal account **[@Saugjunkie2](https://github.com/Saugjunkie2)**, not an anonymous temporary organization.
- Keep the MIT notice (`Copyright (c) 2026 Saugjunkie2`) and the README attribution visible. Confirm rights and the attribution name before making the repository public.
- Pin the repository on the GitHub profile after publication. The project README should remain factual, technical and auditable; avoid claims of security certification or platform-wide validation.
- Use GitHub Releases for a clearly labeled **initial publication of frozen source**. Consider a bundle tag such as `bundle-2026.10` (not a new claim that the Backup and Restore applications share a version number). State Backup `1.6` / Restore `1.14` in the release notes.
- The repository contains substantial historical engineering notes inside the unchanged scripts. `docs/source-provenance.md` distinguishes that history from any newly executed tests.

## Steps to publish (only after owner's review)

1. On GitHub, under the `Saugjunkie2` profile, create a **public** repository named `proxmox-config-backup-restore`. Use the description above and **do not** initialize it with another README, license or `.gitignore` because this package already includes them.
2. **Extract the ZIP first.** The ZIP is a delivery container, **not** a file to upload as the repository. The repository root is the **contents** of the extracted `proxmox-config-backup-restore/` directory: `README.md`, `LICENSE`, `.github/`, `docs/`, `scripts/`, and the other top-level files must appear directly at the GitHub repository root, **not** underneath an extra `proxmox-config-backup-restore/` directory. On Windows, use *Extract All* and open the extracted directory before continuing. Ensure hidden dotfiles and `.github/` are included.
3. Verify `sha256sum -c SHA256SUMS` from the unpacked project root, review the [release checklist](release-checklist.md) and confirm the MIT rights/attribution.
4. **Preferred: publish using Git**, which respects `.gitattributes` and allows the executable-file modes to be recorded explicitly even if ZIP extraction lost them. From the extracted project root, initialize Git and stage **only** the enumerated project paths (do not use an unrestricted `git add -A` in a directory that may contain unrelated local files):

   ```bash
   git init -b main
   git add -- README.md CHANGELOG.md CONTRIBUTING.md SECURITY.md LICENSE \
     docs systemd scripts SHA256SUMS .gitignore .gitattributes .editorconfig .github
   git update-index --chmod=+x scripts/pve-config-backup scripts/pve-config-restore
   git status --short
   git diff --cached --name-only
   git diff --cached --stat
   git commit -m "Publish Proxmox config backup and restore tools"
   git remote add origin https://github.com/Saugjunkie2/proxmox-config-backup-restore.git
   git push -u origin main
   ```

   The `git update-index --chmod=+x` step is intentional: ZIP extraction on Windows (and with some extraction tools) can lose executable-file modes even though the original ZIP records them. This command restores the correct **Git metadata only**, without editing either script's contents. Carefully inspect the staged filename list *before* committing, including for accidentally added confidential material. On Windows, these shell commands are intended for **Git Bash**, not PowerShell. If Git asks for an author identity, configure your own Git name and email; do not use the username here as an invented email address.

   **Browser-upload alternative:** After creating the empty GitHub repository, choose **Add file → Upload files** and upload the extracted project files and directories (including hidden dotfiles and `.github/`), **never the ZIP as one file**. Check that no extra top-level directory was introduced and every file was uploaded. GitHub's browser upload does **not** apply `.gitattributes` and may not retain executable-file modes; prefer Git when exact Git metadata matters. Recheck the published source hashes after cloning. See [GitHub's upload instructions](https://docs.github.com/en/repositories/working-with-files/managing-files/adding-a-file-to-a-repository).

5. Edit the repository **About** section and add the recommended Topics. Verify the license appears as **MIT**, check the source-integrity workflow, and review the public page in a signed-out browser.
6. If desired, pin the project to the GitHub profile and create a GitHub Release accurately describing it as a frozen-source publication. Do not claim the source was retested just because the SHA-256 workflow succeeds.

**Do not upload** real backups, keys, password hashes, diagnostic dumps, private issue attachments or audit packages. Only sanitized, non-sensitive files belong in this public repository.
