# Fail2ban Guide

Add basic intrusion protection by banning clients that trigger suspicious log patterns (e.g., repeated failed logins).

## Install

Debian/Ubuntu

```bash
apt update && apt install -y fail2ban
systemctl enable --now fail2ban
```

RHEL/Alma/Rocky/Fedora

```bash
dnf install -y fail2ban
systemctl enable --now fail2ban
```

## Configure jails

Example: protect SSH.

```ini
# /etc/fail2ban/jail.local
[sshd]
enabled = true

``

Adjust ban times, findtime, and maxretry to your risk tolerance.

## With Docker

- Ensure the relevant logs (Traefik/Caddy) are written to files that fail2ban can read
- Consider using docker log drivers or bind mounts to centralize logs

## Verify

```bash
fail2ban-client status
fail2ban-client status sshd
```

## Tips

- Avoid banning health checks or your own IP ranges
- Ship logs to a central system to correlate bans and alerts


