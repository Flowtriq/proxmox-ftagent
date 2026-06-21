<p align="center">
  <img src="https://flowtriq.com/flowtriq-logo-full.png" alt="Flowtriq" width="280">
</p>

<h3 align="center">Proxmox VE Helper Script for Flowtriq Agent</h3>

<p align="center">
  One-command deploy of <a href="https://github.com/flowtriq/ftagent">ftagent</a> as an LXC container on Proxmox VE.<br>
  Built for the <a href="https://github.com/community-scripts/ProxmoxVE">Proxmox VE Helper-Scripts</a> project.
</p>

<p align="center">
  <a href="https://flowtriq.com"><img src="https://img.shields.io/badge/flowtriq.com-blue?style=flat-square" alt="Website"></a>
  <a href="https://pypi.org/project/ftagent/"><img src="https://img.shields.io/pypi/v/ftagent?style=flat-square&label=ftagent" alt="PyPI"></a>
  <a href="LICENSE"><img src="https://img.shields.io/badge/license-MIT-green?style=flat-square" alt="License"></a>
</p>

---

## What is this?

This repo contains a [Proxmox VE community script](https://community-scripts.github.io/ProxmoxVE/) that creates an LXC container running the **Flowtriq DDoS detection agent**. It handles all dependencies, configuration scaffolding, and systemd service setup automatically.

**Flowtriq Agent (ftagent)** monitors network traffic in real time, detects DDoS attacks across L3/L4/L7, captures forensic PCAPs, and triggers automated mitigation. Ideal for hosting providers, game server networks, and self-hosted infrastructure.

## Quick start

Run this on your Proxmox VE host:

```bash
bash -c "$(curl -fsSL https://raw.githubusercontent.com/flowtriq/proxmox-ftagent/main/ct/ftagent.sh)"
```

The script will:

1. Create a Debian 12 LXC container (1 CPU, 256 MB RAM, 2 GB disk)
2. Install Python 3, libpcap, and tcpdump
3. Install `ftagent` from PyPI with all dependencies
4. Create the config directory and systemd service

After the container is created, enter it and configure:

```bash
# Enter the container (replace ID with the container ID shown after install)
pct enter <ID>

# Run the setup wizard
ftagent --setup

# Start the service
systemctl enable --now ftagent
```

You'll need your **API key** and **Node UUID** from the [Flowtriq dashboard](https://flowtriq.com/dashboard) (Nodes > Add Node).

## Update

Run the script again from inside the container to update ftagent to the latest version:

```bash
bash -c "$(curl -fsSL https://raw.githubusercontent.com/flowtriq/proxmox-ftagent/main/ct/ftagent.sh)"
```

The update function upgrades the pip package and restarts the service.

## What gets installed

| Component | Details |
|---|---|
| **OS** | Debian 12 (Bookworm) |
| **Agent** | `ftagent[full]` from [PyPI](https://pypi.org/project/ftagent/) |
| **System deps** | `python3`, `python3-pip`, `libpcap-dev`, `tcpdump` |
| **Config** | `/etc/ftagent/config.json` |
| **Service** | `ftagent.service` (systemd, auto-restart on failure) |
| **PCAPs** | `/var/lib/ftagent/pcaps` (7-day retention) |
| **Logs** | `journalctl -u ftagent` and `/var/log/ftagent.log` |

## Container defaults

| Setting | Value |
|---|---|
| CPU | 1 core |
| RAM | 256 MB |
| Disk | 2 GB |
| OS | Debian 12 |
| Privileged | Yes (required for `CAP_NET_RAW`) |
| ARM64 | Supported |

All defaults can be overridden in the Proxmox creation dialog.

## Network considerations

The container needs visibility into the traffic you want to monitor. Depending on your setup:

- **Bridge mode (default):** The container sees traffic on its own veth interface. Good for monitoring traffic directed at the container's IP.
- **Host network passthrough:** For monitoring broader host traffic, consider running ftagent directly on the Proxmox host instead, or using a macvlan/bridge with promiscuous mode.
- **Dedicated monitoring interface:** Assign a second NIC to the container for traffic mirroring / SPAN port setups.

## File structure

```
proxmox-ftagent/
  ct/ftagent.sh              # Container creation script (runs on Proxmox host)
  install/ftagent-install.sh  # Install script (runs inside the container)
  json/ftagent.json           # Metadata for the community-scripts website
  LICENSE
  README.md
```

This follows the [community-scripts/ProxmoxVE](https://github.com/community-scripts/ProxmoxVE) convention and is structured for submission to the [ProxmoxVED](https://github.com/community-scripts/ProxmoxVED) development repo.

## Contributing

Contributions welcome. See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

## Links

- [Flowtriq](https://flowtriq.com) - DDoS detection platform
- [ftagent on PyPI](https://pypi.org/project/ftagent/) - Agent package
- [ftagent on GitHub](https://github.com/flowtriq/ftagent) - Agent source
- [Flowtriq Docs](https://flowtriq.com/docs) - Full documentation
- [Proxmox VE Helper-Scripts](https://community-scripts.github.io/ProxmoxVE/) - Community script collection

## License

MIT. See [LICENSE](LICENSE).
