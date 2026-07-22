# Changelog — Homeserver Frontend (`fe-homeserver`)

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

---

## [1.0.0] — 2026-07-22

### Added

- **Component library** — Extracted `Card.astro`, `VitalsBar.astro`, `HeaderControls.astro`, `HistoryCharts.astro` from `index.astro` into `/src/components/`
- **Custom 404 page** — Branded `404.astro` error page with animated server icon, respects dark/light theme preference
- **Nginx custom config** — `nginx.conf` with `gzip` compression, custom 404 routing, and CSP security headers
- **FOUC prevention** — Blocking `<script is:inline>` in `<head>` applies theme class before paint
- **Dark/Light theme toggle** — CSS variable swap on `:root.light-theme`, persisted in `localStorage`
- **Historical analytics charts** — Rolling SVG area charts for CPU, RAM, and Temperature (last 30 samples)
- **Custom favicon** — Branded SVG homeserver logo
- **Auto-refresh polling** — Configurable interval selector (5s / 10s / 30s / 60s / Paused) with manual refresh and status logs
- **Skeleton loaders** — Shimmer placeholders on charts, vitals, and stats tables
- **Sparkline history** — Per-container rolling SVG sparklines

### Changed

- `astro.config.mjs` — Enabled `compressHTML: true` for minified static output
- `Dockerfile` — `COPY nginx.conf` into Nginx release stage

### Fixed

- SVG chart overflow — Y-coordinate clamped to prevent tab overlaps

---

## [0.1.0] — 2026-06-26

### Added

- **Dashboard UI** — Astro-based static site for homeserver monitoring
- **System Vitals Display** — CPU load, RAM usage, disk usage, temperature
- **Docker Container Status** — Real-time container stats and health indicators
- **Git Branch Display** — Shows active branch for public-facing app containers
- **Multi-stage Dockerfile** — Node.js build → Nginx static serving
- **GitHub Actions CI/CD** — Auto-build and push to GHCR on `main` push
- **Environment Configuration** — Build-time injection of `BASE_DOMAIN` and `PUBLIC_API_BASE_URL`
