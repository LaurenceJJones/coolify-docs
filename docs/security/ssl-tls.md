# SSL/TLS Policy

Ensure end-to-end encryption for dashboard and apps. Let’s Encrypt certificates are automatic; harden ciphers and enforce HTTPS.

## Reverse Proxy

- Use Traefik or Caddy managed by Coolify for automatic ACME
- Redirect HTTP to HTTPS; enable HSTS (careful on first enable)
- Prefer TLS 1.2+; disable outdated ciphers

## Certificates

- Use DNS challenge if HTTP challenge is blocked (eg. behind strict firewalls)
- For Cloudflare Orange Cloud, use "Full (strict)" mode with origin certs
- Monitor expiry and renewals via dashboard or external checks

## Client-to-Origin

- If using an external CDN/WAF, secure origin with private networking or allow-lists

## Troubleshooting

- See [/databases/ssl](/databases/ssl) and [/troubleshoot/dns-and-domains/lets-encrypt-not-working](/troubleshoot/dns-and-domains/lets-encrypt-not-working)


