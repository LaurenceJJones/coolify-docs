# CrowdSec Guide

CrowdSec is a collaborative intrusion detection and prevention system (IDPS). This guide shows how to monitor the Coolify reverse proxy (Traefik or Caddy), install the right parsers, and enable blocking with bouncers.

## Blog article

See the official write‑up: [Securing automated app deployment: CrowdSec + Coolify](https://www.crowdsec.net/blog/securing-automated-app-deployment-crowdsec-and-coolify) for an in‑depth walkthrough.

## What you’ll set up

- Reverse proxy log parsing (Traefik or Caddy)
- CrowdSec scenarios for common web attacks
- Optional remediation (host firewall and/or reverse proxy bouncers)

## Prerequisites

- A Coolify host with the built‑in proxy (`coolify-proxy`) running
- Root/sudo access on the host
- Docker installed (for container log acquisition)

## Step 1 — Install CrowdSec

The installer detects your distribution and sets up the repository for you.

```bash
curl -s https://install.crowdsec.net | bash
```

Then install the engine package (pick the command for your distro):

```bash
# Debian/Ubuntu
apt install -y crowdsec

# RHEL/Alma/Rocky/Fedora
dnf install -y crowdsec
```

After installation, CrowdSec will auto‑detect some local services (typically SSH). Because the Coolify proxy runs in a container, we’ll explicitly configure log acquisition for it.

## Step 2 — Install proxy collections

Install the parsers/scenarios for your proxy. Only install the one you actually use.

```bash
cscli hub update
# For Traefik users
cscli collections install crowdsecurity/traefik
# For Caddy users
cscli collections install crowdsecurity/caddy
```

## Step 3 — Enable proxy access logs to stdout

CrowdSec will read logs from the `coolify-proxy` container. Ensure your proxy writes access logs to stdout.

### Traefik

Add the following command flag to your Traefik configuration (for example in your Docker Compose service definition):

```yaml
command:
  - "--accesslog=true"
  - "--ping=true" # likely already present; shown for context
```

Apply the change by restarting the proxy container from the Coolify UI.

### Caddy

Enable access logs in your Caddy configuration so they go to stdout. Example (Caddy v2):

```text
{
  # Global options
  log {
    output stdout
    format json
  }
}

your.site.tld {
  # ... your site config ...
  log {
    output stdout
  }
}
```

Restart the proxy container after updating the configuration:

```bash
docker restart coolify-proxy
```

## Step 4 — Tell CrowdSec where to read logs (acquisitions)

Create `/etc/crowdsec/acquis.d/proxy.yaml` to read Docker logs from the Coolify proxy. Set the `type` to match your proxy.

```yaml
source: docker
container_name:
  - coolify-proxy
labels:
  type: traefik   # or "caddy"
```

Reload CrowdSec to pick up the acquisition change:

```bash
systemctl restart crowdsec
```

## Step 5 — Add remediation (bouncers)

Bouncers enforce decisions made by CrowdSec.

- Host firewall bouncer: blocks at the network level (recommended)
- Reverse proxy bouncer (Traefik or Caddy): blocks at the edge

Generate an API key and install your chosen bouncer(s):

```bash
cscli bouncers add firewall-bouncer --key

# Install a firewall bouncer package
# Debian/Ubuntu (nftables)
apt install -y crowdsec-firewall-bouncer-nftables
# RHEL/Alma/Rocky/Fedora (nftables)
dnf install -y crowdsec-firewall-bouncer-nftables

# Then configure the bouncer with the API key it prints (usually in /etc/crowdsec/bouncers/)
```

Refer to the Traefik/Caddy bouncer documentation if you prefer blocking directly at the proxy.

## Step 6 — Verify it works

```bash
cscli metrics           # check parsers/scenarios ingesting logs
cscli decisions list    # see current bans/decisions
```

You should see the proxy stream in the `metrics` output once traffic flows through it.

## Tips

- Start in alert‑only mode for new scenarios if you’re worried about false positives
- Tune scenarios based on your application traffic profile
- Export alerts to your preferred notification channel

