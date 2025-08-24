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

## References

- UFW Guide: [/security/firewall/guide](/security/firewall/guide)
- Server firewall KB: [/knowledge-base/server/firewall](/knowledge-base/server/firewall)
- Traefik security: [/knowledge-base/proxy/traefik/overview](/knowledge-base/proxy/traefik/overview)
- Caddy security: [/knowledge-base/proxy/caddy/overview](/knowledge-base/proxy/caddy/overview)


