# Firewall

Establish a default-deny network policy to minimize your attack surface. Coolify works well with both host firewalls and cloud/edge firewalls.

## Needed Ports

See the full port guidance in the [server firewall KB](/knowledge-base/server/firewall).

## Linux firewall options (at a glance)

Choose one; both work with Coolify and Docker when configured correctly.

- iptables
  - Widely deployed; Docker programs iptables rules natively.
  - Works out of the box on most distros without extra compatibility layers.

- nftables
  - Modern replacement and default backend on many distros (often via firewalld or UFW).
  - Docker still targets iptables, but most systems ship an iptables-nft compatibility layer so both can coexist.

::: info
If unsure, start with **iptables**. It has the most documentation and is supported natively by Docker.
:::

## Docker and firewalls: important gotchas

- Docker creates its own **NAT** (**N**etwork **A**ddress **T**ranslation) and filter chains, which can bypass **INPUT** rules.
- Apply policy where Docker will see it:
  - Use the `DOCKER-USER` chain to enforce allow/deny rules before Docker’s rules.
  - With UFW, use route rules or helpers like [ufw-docker](https://github.com/chaifeng/ufw-docker) to manage published ports safely.

## UFW in 60 seconds

UFW (Uncomplicated Firewall) is a simple command-line tool that manages the underlying Linux firewall (iptables or nftables) with human-friendly commands.

- Why use it
  - Simple syntax: `ufw allow 80/tcp`
  - Sensible defaults: deny incoming, allow outgoing

- What it does
  - Translates your commands into the correct low-level rule chains
  - Keeps rules persistent across reboots

- Using UFW with Docker
  - Docker-published ports can bypass generic INPUT rules
  - Combine UFW with the `DOCKER-USER` chain or the `ufw-docker` helper to enforce policy on containers

::: warning
Before enabling UFW, make sure you have allowed SSH (your actual port), or you may lock yourself out.
:::

```bash
ufw default deny incoming
ufw default allow outgoing
ufw allow 22/tcp
ufw allow 80/tcp
ufw allow 443/tcp
ufw allow 8000/tcp
ufw allow 6001
ufw allow 6002
ufw enable
```

## Cloud and edge firewalls

- Use your provider’s security groups/firewalls to restrict ingress to 80/443 and your SSH management IPs.
- Prefer deny-all + allow-lists, and consider country/IP reputation filters where available.
- If using Cloudflare/another CDN, consider allowing only their edge IPs to reach your origin, and use TLS “Full (strict)” with origin certs.

## References

- Server firewall KB: [/knowledge-base/server/firewall](/knowledge-base/server/firewall)
- Traefik security: [/knowledge-base/proxy/traefik/overview](/knowledge-base/proxy/traefik/overview)
- Caddy security: [/knowledge-base/proxy/caddy/overview](/knowledge-base/proxy/caddy/overview)


