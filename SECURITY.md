# Security policy and private reporting

## Security expectations

These scripts operate with root privileges and manipulate sensitive Proxmox configuration. They are provided as preserved source snapshots, with no universal-security guarantee. **Read the known caveats in [`docs/security-model.md`](docs/security-model.md)** before attempting any mutating recovery.

## Reporting

**Do not disclose secrets, password hashes, private keys, complete backups or unredacted host logs in public GitHub issues.** When a GitHub repository is published, the owner should enable GitHub Private Vulnerability Reporting (if available) and add an approved contact channel here. **No reporting address has been supplied yet**; do not invent one.

Reports should describe the code version and affected feature, a minimal **synthetic** reproduction, expected vs observed behavior, and any constraints of the test environment. Label claims as source analysis, simulation, external logs, or confirmed real-host tests.

## Support and freeze policy

The current repository is a frozen-source documentation publication. No pledge of fixes, response deadlines or maintenance cadence is implied. The owner may separately decide any future maintenance policy.
