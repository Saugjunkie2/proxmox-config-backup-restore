# Compatibility and scope

[← README](../README.md) · [Limitations](limitations.md)

## Supported scope based on source

| Dimension | What the current source establishes | What it does **not** establish |
| --- | --- | --- |
| Platform | Proxmox VE with `pve-cluster`, native PVE commands, GNU/Linux system utilities | Every PVE or Debian release |
| Node topology | Backup: standalone node; cluster/active guests are refused | Cluster-safe backup or online backup with active guests |
| Guest virtualization | Backup checks QEMU VM and LXC runtime state; VM restore targets QEMU configuration | VM/LXC disk backup or arbitrary guest recovery |
| Backup filesystem | Local timestamped root-private directory | Off-host, encrypted or replicated protection |
| SSH | Specific `sshd_config` and drop-in/include model | All valid OpenSSH layouts |
| Network | ifupdown2-based validation and constrained supported management layouts | All bridge/VLAN/bond/IPv6/network architectures for live apply |
| Firewall | Restore uses `pve-firewall` command path | Proven nftables backend activation equivalence |
| Recovery | Interactive, domain-specific restoration and a standalone emergency DB procedure | Unattended universal restore or recovery from arbitrary destroyed hardware |

## Version observations

The scripts originate from work involving **Proxmox VE 9.x**. This repository packaging did **not** run a new PVE host validation or establish a public cross-version compatibility matrix. The network code contains an exact workaround check for `ifupdown2 3.3.0-1+pmx12` and associated file hashes. That is **not** a blanket claim that every function requires exactly that version, nor a claim of compatibility with other versions.

For versions outside the original environment, treat behavior as **unverified**. Repository integrity verification only confirms the files have not changed.

## Third-party / host dependencies

`bash`, `sqlite3`, `python3`, `perl`, GNU `tar`, coreutils, util-linux, procps, systemd, OpenSSH server binaries, Proxmox-native Perl modules/CLI, and mode-specific tools such as `ifquery`, `ifreload`, `unshare`, `ip`, `pve-firewall`, `qm` and `pct` are used. Consult each script's runtime command gates and source for mode-specific behavior.

## Legacy `Dateifreigabe` tooling

Historical installation notes contained a separate 15-minute temporary direct-root SSH access helper for WinSCP. That helper is **not one of the two published scripts**, is not installed here, and should not be treated as a default dependency. However, the SSH restore helper examines `/run/dateifreigabe/*.conf` in its include contract. A configured host may therefore meet, or fail, the strict SSH restore admission requirements. Document and review that configuration before using option 2. Do not turn on password-based root SSH access merely to satisfy documentation.
