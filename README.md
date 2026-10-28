# FestPass Docker Assessment

- **Name:** Vaibhav Gupta
- **REGNO:** 202401100100271
- **PORT:** 8071
- **Docker Hub username:** vaibhavgupta5
- **Docker Hub repository:** https://hub.docker.com/r/vaibhavgupta5/fest-backend
- **GitHub repository:** https://github.com/vaibhavgupta5/202401100100271-dockerassessment

## AI tools used

- Claude Code (Anthropic): drafted the Dockerfile, compose file, README and this checklist, and explained errors and Docker, Trivy and cosign options. Every command was run by me on my own machine.

## Image

- **Image:** vaibhavgupta5/fest-backend:1.0.0
- **Digest (1.0.0, as pushed):** sha256:178ea9657cbf2c39df80d97e4cf4f265120c5416c5e2a3870553accdf1cbbb56
- **Signed:** yes (1.0.0), verified with cosign.pub
- **Unsigned test release:** vaibhavgupta5/fest-backend:1.0.1 (digest sha256:aef0489908649db6c09a3a09edc4e7bd995eded7201e40c542216bdabb702743), rejected by cosign verify

## Vulnerability table (Trivy)

| Metric | 0.1 (python:3.9) | 1.0.0 (python:3.13.18-slim) |
|---|---|---|
| CRITICAL | 234 | 0 |
| HIGH | 2249 | 49 |
| Image size | 1.6 GB | 211 MB |

Counts are OS packages plus Python packages. Reports: reports/trivy-before.txt and reports/trivy-after.txt.

## Steps not completed

- None yet. Pending: the second tag in step 4.1.
