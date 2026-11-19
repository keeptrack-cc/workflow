# `arc/workflow` — Reusable GitHub Actions

Standard, service-agnostic reusable GitHub Actions workflows (`github.com/keeptrack-cc/workflow`).

## `deploy-vps.yml`

Reusable `workflow_call` pipeline for deploying a Docker Compose service to a VPS over SSH:
1. Checks out the tagged release (`v*`).
2. Syncs the repository to `project_path` via `rsync` (excluding `.git`, `.env`, `.env.*`, plus optional `rsync_excludes`).
3. Optionally writes `secrets.ENV_FILE` to `<project_path>/.env` (`chmod 600`) when provided by the caller repo.
4. Optionally opens `port/tcp` in UFW, rebuilds `service_name` with Docker Compose (`--no-cache`), and restarts it.

### Required & Optional Secrets
- `USER` (required): VPS SSH username
- `HOST` (required): VPS hostname or IP
- `SSH_KEY` (required): Private SSH key
- `ENV_FILE` (optional): Raw `.env` contents for the target service

### Example Caller (`.github/workflows/deploy-in-VPS.yml`)

```yaml
name: Deploy Service to VPS

on:
  push:
    tags:
      - 'v*'

jobs:
  deploy:
    uses: keeptrack-cc/workflow/.github/workflows/deploy-vps.yml@main
    with:
      service_name: keeptrack-auth
      project_path: project/keeptrack/auth
      port: "5201"
    secrets: inherit
```
