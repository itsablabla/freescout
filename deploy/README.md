# FreeScout VPS deployment

Single-host Docker Compose stack for FreeScout (app + MariaDB).

## Prerequisites

- Docker Engine 24+ and the Compose plugin
- A DNS record pointing at the host
- A reverse proxy terminating TLS (Caddy, nginx, Traefik) that forwards to `APP_PORT`

## Setup

```bash
cp deploy/.env.example deploy/.env
# Edit deploy/.env — set APP_URL, DB and admin passwords, SMTP creds.

docker compose -f deploy/docker-compose.yml --env-file deploy/.env up -d
docker compose -f deploy/docker-compose.yml --env-file deploy/.env logs -f app
```

First boot can take 2–5 minutes while the schema is created and assets are
warmed. Once the `app` container is healthy, FreeScout will be reachable at
`APP_URL` (the bootstrap admin is created automatically from
`ADMIN_EMAIL` / `ADMIN_PASSWORD`).

## Operations

- Update the image: bump `FREESCOUT_IMAGE_TAG` in `deploy/.env`, then
  `docker compose -f deploy/docker-compose.yml --env-file deploy/.env pull && ... up -d`.
- App state (sessions, cache, uploaded files, installed Modules, generated
  config) lives in `freescout_data` (`/data` inside the container). Logs live
  in `freescout_logs`, and the database in `freescout_db`. Back all three up
  off-box.
- Container image:
  [`nfrastack/freescout`](https://github.com/nfrastack/container-freescout)
  (the canonical successor to the legacy `tiredofit/freescout` image).
- Any additional FreeScout `.env` setting can be injected with the
  `FREESCOUT_` prefix — e.g. `FREESCOUT_APP_TRUSTED_PROXIES=10.0.0.0/8` is
  written into FreeScout's config as `APP_TRUSTED_PROXIES=10.0.0.0/8`.
