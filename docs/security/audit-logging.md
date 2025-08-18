# Audit Logging

Track changes and access to support investigations and compliance.

## What to Log

- User logins, 2FA events, and role changes
- Project, service, and environment variable modifications
- Deployment, scale, and backup operations

## Shipping Logs

- Stream logs to external systems (Loki/Promtail, ELK/OpenSearch, Datadog)
- Retain at least 90 days for incident review

## Alerting

- Create alerts for admin actions, failed logins, and unexpected redeploys


