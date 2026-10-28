# FestPass Docker Assessment

FestPass is the registration app for a college tech fest. It has three parts: a web page (frontend), a Python API (backend) and a PostgreSQL database. This repository containerises all three, scans and fixes the backend image, publishes it to Docker Hub with a version, and signs it with cosign.

## Details

| Item | Value |
|---|---|
| Name | Vaibhav Gupta |
| REGNO | 202401100100271 |
| PORT | 8071 |
| Docker Hub username | vaibhavgupta5 |
| Docker Hub repository | https://hub.docker.com/r/vaibhavgupta5/fest-backend |
| GitHub repository | https://github.com/vaibhavgupta5/202401100100271-dockerassessment |

## AI tools used

- Claude Code (Anthropic): drafted the Dockerfile, compose file, frontend page, README and overview, and explained errors and Docker, Trivy and cosign options. Every command was run by me on my own machine.

## Quick start

1. Create the local environment file. It holds the database password and is never committed:

   ```bash
   cp .env.example .env
   ```

   Replace `change-me` in `.env` with a real password.

2. Start the stack:

   ```bash
   docker compose up -d
   ```

3. Open the app at http://localhost:8071

4. Stop the stack. The database volume keeps the registrations:

   ```bash
   docker compose down
   ```

## Architecture

```
browser ──▶ frontend (nginx, :8071)
              │  frontend-net
              ▼
            backend (Flask, :5000)  ──▶  db (PostgreSQL 17, data in volume pgdata)
              │  backend-net
```

- Only the frontend publishes a port. The database is reachable from the backend only.
- The frontend cannot reach the database: `frontend` is on `frontend-net` alone.
- The backend waits for `db` to pass a `pg_isready` healthcheck before it starts.
- The backend image is pulled from Docker Hub: `vaibhavgupta5/fest-backend:1.0.0`.

## API

| Method | Path | Purpose |
|---|---|---|
| GET | `/api/health` | Service, REGNO, version, container id and database status |
| GET | `/api/registrations` | List all registrations |
| POST | `/api/registrations` | Add one registration. Body: `{"name": "Asha", "event": "Hackathon"}` |

## Image

- Image: `vaibhavgupta5/fest-backend:1.0.0`
- Digest (1.0.0, as pushed): `sha256:178ea9657cbf2c39df80d97e4cf4f265120c5416c5e2a3870553accdf1cbbb56`
- Base: `python:3.13.18-slim`, running as the non-root user `app` (UID 10001)
- Version is baked in at build time through the `APP_VERSION` build argument
- Signed with cosign (key pair in `cosign.pub`). The signature is verified against `cosign.pub`.

Verify the signature:

```bash
cosign verify --key cosign.pub --insecure-ignore-tlog=true vaibhavgupta5/fest-backend:1.0.0
```

The signature is not uploaded to the public Rekor transparency log, so verification uses `--insecure-ignore-tlog`.

## Vulnerability table (Trivy)

| Metric | 0.1 (python:3.9) | 1.0.0 (python:3.13.18-slim) |
|---|---|---|
| CRITICAL | 234 | 0 |
| HIGH | 2249 | 49 |
| Image size | 1.6 GB | 211 MB |

Counts cover OS packages plus Python packages. Full reports: `reports/trivy-before.txt` and `reports/trivy-after.txt`.

Check that no fixable CRITICAL vulnerability remains:

```bash
trivy image --severity CRITICAL --ignore-unfixed --exit-code 1 vaibhavgupta5/fest-backend:1.0.0
```

## Build and publish

```bash
docker build --build-arg APP_VERSION=1.0.0 -t vaibhavgupta5/fest-backend:1.0.0 backend
docker push vaibhavgupta5/fest-backend:1.0.0
cosign sign --key cosign.key --use-signing-config=false --tlog-upload=false vaibhavgupta5/fest-backend@sha256:178ea9657cbf2c39df80d97e4cf4f265120c5416c5e2a3870553accdf1cbbb56
```

An unsigned test release, `vaibhavgupta5/fest-backend:1.0.1`, was pushed to show that `cosign verify` rejects an unsigned image.

## Repository layout

```
.
├── README.md
├── DOCKERHUB_OVERVIEW.md
├── compose.yaml
├── .env.example
├── .gitignore
├── cosign.pub
├── backend/
│   ├── app.py
│   ├── requirements.txt
│   ├── Dockerfile
│   └── .dockerignore
├── frontend/
│   ├── index.html
│   └── nginx.conf
├── reports/
│   ├── trivy-before.txt
│   └── trivy-after.txt
└── screenshots/
```

## Steps not completed

- None yet. Pending: the second tag in step 4.1 (`vaibhavgupta5/fest-backend:1.0` or another name).
