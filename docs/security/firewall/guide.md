# Firewall Guide

Step-by-step guidance to lock down your Coolify host and published services.

## 1) Decide your control points

- Host firewall: enforce default-deny and only open the ports you need
- Reverse proxy: publish 80/443 only, keep app containers on private networks
- Cloud/edge firewall: restrict to 80/443 and your SSH management IPs

## 2) UFW quick start (Debian/Ubuntu)

```bash
ufw default deny incoming
ufw default allow outgoing
ufw allow 22/tcp           # SSH
ufw allow 80/tcp           # HTTP (ACME/redirect)
ufw allow 443/tcp          # HTTPS
ufw enable
```

With Docker

- Docker-published ports can bypass generic INPUT rules.
- Use the `DOCKER-USER` chain or helper tools like `ufw-docker` to enforce policy on container traffic.

## 3) firewalld quick start (RHEL/Alma/Rocky/Fedora)

```bash
firewall-cmd --permanent --set-default-zone=public
firewall-cmd --permanent --add-service=ssh
firewall-cmd --permanent --add-service=http
firewall-cmd --permanent --add-service=https
firewall-cmd --reload
```

With Docker

- Ensure docker interfaces are placed in the expected zone or add rich rules for docker networks.

## 4) Enforce policy before Docker rules (iptables)

Docker inserts its own chains. Use `DOCKER-USER` to apply allow/deny first.

```bash
# Example: allow only 80/443 inbound to the host, drop everything else
iptables -I DOCKER-USER -p tcp --dport 80 -j ACCEPT
iptables -I DOCKER-USER -p tcp --dport 443 -j ACCEPT
iptables -A DOCKER-USER -j DROP
```

Persist these rules using your distro’s mechanisms (e.g., `iptables-save` or a systemd unit).

## 5) Cloud and edge firewall patterns

- Deny-all + allow only 80/443 to the reverse proxy and your SSH IPs
- If using a CDN/WAF (e.g., Cloudflare), allow only their edge IPs to reach your origin
- Prefer TLS “Full (strict)” with origin certificates

## 6) Validate and monitor

- Validate from outside: `nmap -Pn -p 22,80,443 your.domain`
- Watch logs for drops/blocks and tune rules to avoid false positives

## Troubleshooting tips

- Locked out over SSH? Most providers offer a web console to revert firewall changes
- Publishing a new port via Docker and it’s not reachable? Check `DOCKER-USER` and host firewall rules first
- ACME/Let’s Encrypt failing? Temporarily allow HTTP (80) and confirm DNS/edge firewall paths


