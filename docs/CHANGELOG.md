# Changelog — Homeserver Frontend (`fe-homeserver`)

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

---

## [1.0.0] — 2026-06-26

### Added

- **Dashboard UI** — Astro-based static site for homeserver monitoring
- **System Vitals Display** — CPU load, RAM usage, disk usage, temperature
- **Docker Container Status** — Real-time container stats and health indicators
- **Git Branch Display** — Shows active branch for public-facing app containers
- **Multi-stage Dockerfile** — Node.js build → Nginx static serving
- **GitHub Actions CI/CD** — Auto-build and push to GHCR on `main` push
- **Environment Configuration** — Build-time injection of `BASE_DOMAIN` and `PUBLIC_API_BASE_URL`
