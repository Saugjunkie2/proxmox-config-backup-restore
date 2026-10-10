# Frozen source provenance and version semantics

[← README](../README.md) · [Release checklist](release-checklist.md)

## Unmodified source snapshots

The publication project deliberately copies the **byte-identical** uploaded source into conventional GitHub filenames. Neither source was translated, refactored, patched nor behaviorally changed in this packaging effort.

| Original uploaded filename | Repository location | Internal version | SHA-256 |
| --- | --- | --- | --- |
| `pve-config-backup(8)` | [`scripts/pve-config-backup`](../scripts/pve-config-backup) | `SCRIPT_VERSION="1.6"` | `1a627bccc2a9eb5a5f45d5a178b446b2fc6c7bcb54df19443f4a28df68bf5d1d` |
| `pve-config-restore(20261010-114444)` | [`scripts/pve-config-restore`](../scripts/pve-config-restore) | `SCRIPT_VERSION="1.14"` | `43170fbb3d412ad8cffe4706a13941eb7054636bb9783fccc3b8393dc47c62c5` |

These are SHA-256 hashes of the raw file contents. Repository filenames, filesystem modes and Git metadata do not alter the contents. `SHA256SUMS` at the repository root lists the two repository-relative paths.

## Revision labels

Both scripts retain detailed internal development history. In particular, backup 1.6 contains a `1.6-B10v2-R2 (ENTWURF)` heading, while restore 1.14 contains historical `DRAFT/ENTWURF` labels. Those labels are **original source content**, not a new claim that code was modified for GitHub. The Restore source also retains one historical mention of the original node name `localserver001` in a comment; it is **not** an endpoint or a configured runtime address. Removing it would break the explicit byte-for-byte freeze, so the publisher should knowingly accept or separately authorize a changed-source release. This repository does not silently rewrite release history or infer validation from those headings.

## Documentation provenance

A separate legacy German-language installation transcript was reviewed to understand intended paths and old workflows. It was **not shipped as a public installation document**: portions describe superseded SQLite handling, a problematic historic systemd unit dependency, and an additional direct-root SSH sharing helper that is not one of the two scripts. The public English documentation is based on the shipped source behavior, with limitations explicitly marked.

## Verification discipline

File identity is reproducible by `sha256sum -c SHA256SUMS` or `sha256sum scripts/...`. This checks **identity only**, not operational safety. Existing field tests, earlier external review assertions and current repository packaging must never be conflated.
