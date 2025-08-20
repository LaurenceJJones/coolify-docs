# Coolify Firewall Guide (UFW only)

A simple, copy-paste guide to lock down a Coolify host while keeping your apps reachable.

## What you will expose

* SSH on port **22** (management)
* HTTP on port **80** (ACME and redirects)
* HTTPS on port **443** (apps via the reverse proxy)

Everything else stays closed.

If your hosting provider offers a cloud firewall such as Hetzner, you can skip the UFW steps. Use the provider firewall instead. It gives the same protection and is easier to manage from the provider website than from the terminal.

---

## 1) Install and reset UFW (Debian/Ubuntu)

```bash
sudo apt update
sudo apt install -y ufw

# Start from safe defaults
sudo ufw --force reset
sudo ufw default deny incoming
sudo ufw default allow outgoing
```

---

## 2) Allow the needed ports, then enable

**Add SSH before enabling, or you may lock yourself out.**

:::warning
if you have configured ssh to listen on a different port then please update **22/tcp** to the correct port
:::

```bash
# SSH (open to the world)
sudo ufw allow 22/tcp

# SSH (safer: only from your IP — replace 203.0.113.10)
# sudo ufw allow from 203.0.113.10 to any port 22 proto tcp

# Web
sudo ufw allow 80/tcp
sudo ufw allow 443 ## tcp and udp for http3

# Enable and check
sudo ufw enable
sudo ufw status verbose

```

---

## 3) Using UFW with Docker and Coolify

Docker programs its own firewall rules, which can bypass generic UFW INPUT rules. To keep your “only 22/80/443” policy intact:

* Publish only what you actually need through Coolify (prefer routing apps via the proxy on 80/443).
* Keep UFW defaults as above to block everything else.
* **If you want UFW to enforce rules on Docker-published ports, use the helper script *ufw-docker* (community tool). It makes UFW apply to container traffic so your allow/deny rules are respected.**
  → Link: [https://github.com/chaifeng/ufw-docker](https://github.com/chaifeng/ufw-docker)

**Example (not routed through the proxy):** you can allow Coolify dashboard ports **8000**, **6001**, and **6002** directly.

```bash
# Open only the ports you intend to publish
sudo ufw allow 8000/tcp
sudo ufw allow 6001:6002/tcp

# After installing and configuring ufw-docker (see link above),
# these UFW rules will also govern traffic to Docker-published ports.
```

Tip: If a published app isn’t reachable, run `sudo ufw status numbered` to confirm the rules exist, and verify whether it’s routed via the proxy or exposed as a host port.

---

## 4) Checking blocked connections

By default UFW does not record blocked connections to reduce noise. If you need to find out why a connection is not allowed, you can turn logging on.

### Enable logging:

```bash
sudo ufw logging on
sudo ufw reload
```

### View dropped connections:

```bash
sudo journalctl -k -f
```

`-k` filters to `kernel` logs, which is where UFW writes its messages.

`-f` tells `journalctl` to follow new entries. Leave this running while you try the connection from your application so you can see blocked attempts in real time.

### Understanding the logged information:

The log line may look complicated, however, here is a quick breakdown of the most vital information:

```
Aug 20 19:27:30 bookworm kernel: [UFW BLOCK] IN=br-17a84c85ad7d OUT= PHYSIN=veth949879c MAC=02:42:2c:5f:ed:c1:02:42:0a:00:01:05:08:00 SRC=10.0.1.5 DST=10.0.0.1 LEN=60 TOS=0x00 PREC=0x00 TTL=64 ID=10641 DF PROTO=TCP SPT=38686 DPT=23517 WINDOW=64240 RES=0x00 SYN URGP=0
```

`SRC=10.0.1.5`: Source IP address that initiated the connection

`DST=10.0.0.1`: Destination IP address on your server

`SPT=38686`: Source port used by the client, usually an ephemeral port

`DPT=23517`: Destination port on your server the client tried to reach

### Disable logging:

After you have finished troubleshooting, turn logging off:

```bash
sudo ufw logging off
sudo ufw reload
```

## 5) Optional: add a cloud firewall

Mirror the same rules at your provider:

* Allow **22** (optionally only from your management IP).
* Allow **80** and **443**.
* Deny everything else.

If you use a CDN/WAF, you can further restrict inbound traffic to the provider’s edge IP ranges (advanced, optional).

---

## 6) Validate

From another machine:

```bash
nmap -Pn -p 22,80,443 your.domain
curl -I https://your.domain
```

On the server:

```bash
sudo ufw status verbose
sudo ss -tulpn
```

---

## Troubleshooting

* **Locked out of SSH**: Use your VPS provider’s web console and run `sudo ufw allow 22/tcp` or `sudo ufw disable`.
* **App not reachable**: Route via the Coolify proxy on 80/443 and confirm the container is healthy. Avoid exposing extra host ports unless you need them.
* **Let’s Encrypt issues**: Keep **80** open during certificate issuance and confirm DNS/cloud-firewall paths.
* **Rules look right but traffic still passes**: Review any tools that modify Docker’s firewall behavior. Keeping to UFW plus the proxy model is the simplest path.
