# Intrusion Detection & Prevention (IDPS)

Add defense-in-depth with log- and behavior-based detection to block brute force and known-bad IPs.

## Options

- CrowdSec: collaborative IP reputation, remediations for firewalls and reverse proxies — see [CrowdSec Guide](/security/idps/crowdsec)
- Fail2ban: local log pattern detection and bans — see [Fail2ban Guide](/security/idps/fail2ban)
- Cloudflare WAF/Rate Limiting: managed service at the edge

## Deployment Patterns

- Reverse proxy integration (Traefik/Caddy) to ban at the edge
- Host firewall bouncer (nftables/iptables) for system-wide protection
- Combine with provider edge firewall to stop before reaching host

## CrowdSec Quick Start

Full setup and Docker log acquisition details: [CrowdSec Guide](/security/idps/crowdsec)

## Fail2ban Quick Start

Complete installation and jail configuration: [Fail2ban Guide](/security/idps/fail2ban)

## Tips

- Forward proxy logs to a consistent location for easier parsing
- Validate that bans do not block health checks or internal networks
- Export alerts to your notification channel (Slack/Email/Webhook)


