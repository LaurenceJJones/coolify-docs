# Secrets Management

Protect credentials, API keys, and tokens used by Coolify and your services.

## Principles

- Never hardcode secrets in images or commit them to git
- Use environment variables or mounted files from secure storage
- Rotate secrets regularly and after incidents

## In Coolify

- Store app/service env vars in Coolify projects
- Use S3-backed backups for encrypted at-rest storage
- Restrict who can view and edit secrets via roles

## External Secret Stores

- Consider an external secrets manager if you need centralized rotation (eg. 1Password, HashiCorp Vault, Doppler, AWS/GCP/Azure managers)
- Sync secrets into runtime via CI/CD or sidecar pattern

## Transport & Storage

- Enforce TLS for all secret transport
- Keep backup copies encrypted and off-host


