# Access Control (Users, Teams, SSH)

Control who can access Coolify and servers, with the least privilege required.

## Coolify Users & Teams

- Enforce unique accounts; avoid shared logins
- Enable and require 2FA for admin and maintainer roles
- Use roles and project-level permissions to limit blast radius

## SSO/OAuth

- Prefer SSO via GitHub/GitLab/… when available
- Periodically audit authorized apps and tokens
- See [/knowledge-base/oauth](/knowledge-base/oauth)

## SSH Access to Servers

- Use key-based auth; disable password auth
- Restrict SSH to maintainer IPs via firewall
- Consider short-lived SSH certificates (eg, smallstep) for ephemeral access
- Log and alert on sudo and SSH logins

## API Access

- Rotate API tokens; scope them narrowly
- Store tokens in a secrets manager


