# Server & Docker Hardening

Reduce risk by hardening the OS, Docker, and Coolify configuration. Start with a minimal base and keep it patched.

## Operating System

- Apply security updates regularly (enable unattended-upgrades or similar)
- Enforce strong SSH: key-based auth, disable password auth, optional port knock/2FA
- Restrict sudo access to least privilege and require MFA where possible
- NTP/time sync enabled for accurate logs and TLS

## Docker Engine

- Keep Docker up to date
- Use rootless Docker where applicable, or limit container privileges
- Avoid `:latest` images; pin versions and track changelogs
- Use separate Docker networks; do not expose containers directly
- Limit capabilities, use read-only filesystems, and drop `--privileged`

## Coolify Configuration

- Use strong admin credentials and 2FA for users
- Restrict dashboard access by IP where possible at the reverse proxy
- Keep backups off-host and encrypted; validate restores
- Rotate API tokens and SSH keys regularly

## References

- Terminal access: [/knowledge-base/internal/terminal](/knowledge-base/internal/terminal)
- Backups: [/databases/backups](/databases/backups)
- OAuth/SSO: [/knowledge-base/oauth](/knowledge-base/oauth)


