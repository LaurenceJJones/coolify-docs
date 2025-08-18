# CrowdSec Guide

Collaborative intrusion detection and prevention with Remediations for firewalls and reverse proxies.

## Blog Article

See [CrowdSec Blog Article](https://www.crowdsec.net/blog/securing-automated-app-deployment-crowdsec-and-coolify) for a in depth guide on deploying CrowdSec with Coolify.

## Short Guide

### Install

#### Repository

Debian/Ubuntu

```bash
curl -s https://install.crowdsec.net | bash
```

RHEL/Alma/Rocky/Fedora

```bash
curl -s https://install.crowdsec.net | bash
```

#### Package

Debian/Ubuntu

```bash
apt install crowdsec -y
```

RHEL/Alma/Rocky/Fedora

```bash
dnf install crowdsec -y
```

Upon installation CrowdSec will detect and find services to monitor, in this case it will be just SSH since the proxy is running inside of a container CrowdSec cannot detect it and we will have to configure this manually.

### Collections

Enable parsers/scenarios relevant to your stack:

```bash
cscli hub update
cscli collections install crowdsecurity/traefik
cscli collections install crowdsecurity/caddy
```

### Acquisitions

Acquisitions inform CrowdSec where to find log files to monitor, in this case we will inform CrowdSec to find the Coolify proxy.

```yaml
source: docker
container_name:
  - coolify-proxy
labels:
  type: <type>
```

Change `<type>` to be either `caddy` or `traefik` depending on which reverse proxy you are using from Coolify.

### Configure Logging

By default the Coolify proxy

### Remediation Components

- Firewall bouncer (nftables/iptables) to block at the host level
- Reverse proxy bouncer for Traefik or Caddy to block at the edge

```bash
cscli bouncers add my-firewall-bouncer --key
# then install the relevant bouncer and configure with the key
```

### Verify

```bash
cscli metrics
cscli decisions list
```

## Tips

- Tune scenarios to reduce false positives; test in alert-only mode first if needed
- Export alerts to your notification channels


