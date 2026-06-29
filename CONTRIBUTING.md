# Contributing

Thanks for your interest in improving the Proxmox ftagent script.

## Reporting issues

Open an issue on this repo with:

- Your Proxmox VE version
- The error output (if applicable)
- Steps to reproduce

## Submitting changes

1. Fork the repo and create a feature branch
2. Make your changes
3. Test against a real Proxmox VE instance
4. Open a pull request with a clear description of what changed and why

## Script conventions

This repo follows the [community-scripts/ProxmoxVE](https://github.com/community-scripts/ProxmoxVE) conventions:

- Use `$STD` before commands that produce noisy output
- Use `msg_info` / `msg_ok` / `msg_error` for status messages
- Use heredocs for writing config files
- No `sudo` (containers run as root)
- Use `apt-get` for scripted installs (not `apt`, which is for interactive use)
- Quote all variables

## Code of conduct

Be respectful and constructive. We're all here to make self-hosting easier.
