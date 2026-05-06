---
name: docker-compose-app-install
description: Use this skill when installing, adding, deploying, or integrating a new self-hosted application into an existing Docker Compose stack on a production or homelab server. Preserve existing services, avoid global restarts, create backups, validate compose syntax, optimize resources, and provide rollback.
---

# Docker Compose App Install

Use this skill only when the task is to add a new application to an existing Docker Compose stack.

## Goal

Install the requested self-hosted app safely, cleanly, and minimally inside the existing Docker Compose environment.

## Hard rules

- Do not run `docker compose down` globally.
- Do not stop, restart, recreate, rename, remove, prune, or modify unrelated containers.
- Do not modify unrelated services, ports, volumes, networks, labels, or environment variables.
- Do not delete existing data.
- Do not use destructive cleanup commands.
- Do not expose public ports unless required.
- Do not expose database ports.
- Prefer official images and official installation documentation.
- Prefer pinned stable image tags over `latest`, unless the user explicitly requests `latest`.
- Prefer existing reverse proxy integration if one is detected.
- If no reverse proxy exists, bind the new app to `127.0.0.1` when possible.
- Always create backups before editing compose or env files.
- Always validate with `docker compose config`.
- Always start only the new service and its direct dependencies.
- Always provide rollback commands.

## Required workflow

### 1. Discover the stack

Inspect the current directory and nearby files.

Find:

- `docker-compose.yml`
- `compose.yml`
- `.env`
- override compose files
- existing services
- existing volumes
- existing networks
- existing published ports
- existing reverse proxy

Detect whether the stack uses:

- Traefik
- Nginx Proxy Manager
- Caddy
- SWAG
- plain Nginx
- no reverse proxy

Use:

```bash
pwd
ls -la
docker compose ps
docker compose config --services
