# FreeScout VPS deployment

Single-host Docker Compose stack for FreeScout (app + MariaDB).

## Prerequisites

- Docker Engine 24+ and the Compose plugin
- A DNS record pointing at the host
- A reverse proxy terminating TLS (Caddy, nginx, Traefik) that forwards to `APP_PORT`

## Setup

```bash
cp deploy/.env.example deploy/.env
# Edit deploy/.env — set APP_URL/APP_HOST, DB and admin passwords, SMTP creds.

docker compose -f deploy/docker-compose.yml --env-file deploy/.env up -d
docker compose -f deploy/docker-compose.yml --env-file deploy/.env logs -f app
```

Then browse to `APP_URL` and finish the FreeScout web installer (it picks up
the DB credentials and admin user from the container environment).

## Operations

- Update the image: bump `FREESCOUT_IMAGE_TAG` in `deploy/.env`, then
  `docker compose -f deploy/docker-compose.yml --env-file deploy/.env pull && ... up -d`.
- App data is stored in the `freescout_data`, `freescout_logs`, and
  `freescout_db` named volumes. Back these up off-box.
- Container image: [`tiredofit/freescout`](https://github.com/tiredofit/docker-freescout).
