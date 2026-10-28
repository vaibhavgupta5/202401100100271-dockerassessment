# fest-backend

Backend API for FestPass, the registration app for a college tech fest. A small Flask service that stores registrations in PostgreSQL.

- **Repository:** vaibhavgupta5/fest-backend
- **Latest version:** 1.0.0 (signed with cosign)
- **Base image:** python:3.13.18-slim
- **Runs as:** non-root user `app` (UID 10001)
- **Listens on:** port 5000

## Tags

| Tag | Status | Notes |
|---|---|---|
| 1.0.0 | Signed | Recommended. Fixed base image, 0 CRITICAL vulnerabilities in the Trivy scan |
| 1.0.1 | Unsigned | Test release. `cosign verify` rejects it |

Use explicit version tags in production. Do not use `latest`.

## Quick start

Run the backend with a PostgreSQL database reachable at `DB_HOST`:

```bash
docker run -d --name backend -p 5000:5000 \
  -e REGNO=202401100100271 \
  -e DB_HOST=db -e DB_NAME=festpass -e DB_USER=festpass -e DB_PASSWORD=change-me \
  vaibhavgupta5/fest-backend:1.0.0
```

Or use the full stack in the GitHub repository: https://github.com/vaibhavgupta5/202401100100271-dockerassessment

## Environment variables

| Variable | Default | Meaning |
|---|---|---|
| `REGNO` | `NOT-SET` | Registration number shown by the health endpoint |
| `APP_VERSION` | `dev` (set to `1.0.0` in the 1.0.0 image) | Version shown by the health endpoint |
| `DB_HOST` | `localhost` | PostgreSQL host |
| `DB_NAME` | `festpass` | Database name |
| `DB_USER` | `festpass` | Database user |
| `DB_PASSWORD` | empty | Database password. Never bake it into the image |

## Endpoints

| Method | Path | Purpose |
|---|---|---|
| GET | `/api/health` | Service status, REGNO, version, container id and database connection |
| GET | `/api/registrations` | List all registrations |
| POST | `/api/registrations` | Add a registration. Body: `{"name": "Asha", "event": "Hackathon"}` |

## Verify the signature

```bash
cosign verify --key cosign.pub --insecure-ignore-tlog=true vaibhavgupta5/fest-backend:1.0.0
```

The public key `cosign.pub` is in the GitHub repository. The signature is not uploaded to the public Rekor transparency log, so verification skips the tlog check.

## Digest

- 1.0.0: `sha256:178ea9657cbf2c39df80d97e4cf4f265120c5416c5e2a3870553accdf1cbbb56`

## Security notes

- Built from `python:3.13.18-slim`, not `latest`.
- Scanned with Trivy 0.76.1. No CRITICAL vulnerabilities in 1.0.0.
- The container runs as a non-root user.
- Only the Docker Hub images from this repository should be trusted. Check the signature before you deploy.
