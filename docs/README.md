# Homeserver Frontend (`fe-homeserver`)

> Astro-based static dashboard for monitoring the homeserver — served via Nginx.

## Overview

A lightweight Astro static site that serves as the homeserver dashboard. It provides a visual interface for monitoring system vitals (CPU, RAM, disk, temperature) and Docker container statuses by consuming the backend API (`be-homeserver`).

<img display="blocl" margin="auto" width="100%" height="100%" alt="screencapture-dashboard-fahmiefendy-dev-2026-07-10-10_52_50" src="https://github.com/user-attachments/assets/a3db9a7b-9ef3-4137-b6e8-23b9c5179c7a" />

## Features

- **Public Apps grid** — cards for each publicly deployed app (Homeserver Dashboard, The Wine Corner, Your Places, Kei Japanese Toast, Electricity Tracker) linking to their live URLs, with an online/offline status dot per container (FE/BE where applicable).
- **Internal Tools grid** — quick links to Openinary, Portainer, Grafana, and Prometheus, each with a live status dot.
- **Infrastructure grid** — status-only cards (no external link) for Nginx, MySQL, MongoDB, PostgreSQL, Watchtower, Loki, Promtail, cAdvisor, and Node Exporter.
- **Live system vitals bar** — CPU load, RAM used/total, disk usage, and temperature, polled from `be-homeserver`. A toggle switches between a compact single-line view and an expanded view with sparkline history for CPU, RAM, and temperature.
- **Historical analytics charts** — Rolling SVG area charts (last 30 samples) for CPU, RAM, and Temperature with tab switching and a loading skeleton while data accumulates.
- **Auto-refresh polling** — Configurable interval selector (5s / 10s / 30s / 60s / Paused) with a last-synced timestamp and manual refresh button.
- **Per-container stats** — a toggle reveals CPU%, memory%, memory usage, and uptime for every container, each with a rolling CPU/memory sparkline (last 12 samples).
- **Status dots** — any card with a `data-dot-for` attribute turns green when the matching container's Docker status starts with `Up`, red otherwise.
- **Dark/Light theme toggle** — CSS variable-based theming, persisted in `localStorage`. Theme is applied before first paint to prevent flicker.
- **Error states** — red banner when the backend API is unreachable; yellow alert when the Docker daemon is inaccessible.
- **Skeleton loaders** — shimmer placeholders on all vitals and container stat cells while data is loading.
- **Custom 404 page** — branded error page matching the dashboard theme.
- **Gzip compression** — Nginx serves assets with gzip for reduced payload size.

All data fetching happens client-side in the browser — the page itself is a single static HTML file with no server-side rendering or API routes.

## Tech Stack

| Layer | Technology |
|-------|------------|
| Framework | [Astro](https://astro.build/) v6.x |
| Runtime | Node.js ≥ 22.12.0 (build only) |
| Serving | Nginx Alpine (static files) |
| Build Output | Static HTML/CSS/JS (`./dist/`) |

## Architecture

```
┌────────────────────────────────────────┐
│           fe-homeserver                │
│  ┌──────────────────────────────────┐  │
│  │  Nginx Alpine (:80)             │  │
│  │  └── /usr/share/nginx/html/     │  │
│  │      └── Astro static build     │  │
│  └──────────────────────────────────┘  │
│                                        │
│  Build Stage (multi-stage Dockerfile): │
│  • node:22-alpine → npm run build     │
│  • nginx:alpine   → serve dist/       │
│                                        │
│  API Calls (client-side):              │
│  • GET {PUBLIC_API_BASE_URL}/vitals    │
│  • GET {PUBLIC_API_BASE_URL}/docker    │
└────────────────────────────────────────┘
```

> **Note:** API calls are made from the **browser** (client-side JavaScript), not server-side. The frontend does not communicate with the backend inside Docker — it goes through the external Nginx reverse proxy.

## Environment Variables

| Variable | Required | Description | Example |
|----------|----------|-------------|---------|
| `BASE_DOMAIN` | Yes | Base domain for app links and redirects | `fahmiefendy.dev` |
| `PUBLIC_API_BASE_URL` | Yes | Public URL of the backend API | `https://api-dashboard.fahmiefendy.dev` |

> **Important:** These variables are injected at **build time** (Astro static generation). Changing them requires rebuilding the Docker image.

## Local Development

```bash
# Install dependencies
npm install

# Start dev server (hot-reload)
npm run dev
# → http://localhost:4321

# Build for production
npm run build

# Preview production build
npm run preview
```

### Local Environment Setup

```bash
# Create local env file
cp .env.example .env

# Edit for local development
# PUBLIC_API_BASE_URL=http://localhost:3002
```

## Deployment

### CI/CD Pipeline

1. Push to `main` branch triggers GitHub Actions workflow
2. Docker image is built with build args:
   - `BASE_DOMAIN` from GitHub repository variables
   - `PUBLIC_API_BASE_URL` from GitHub repository variables
3. Image pushed to `ghcr.io/fahmiefendy/fe-homeserver`
4. Tagged with `latest` and commit SHA
5. Watchtower on the homeserver detects and auto-updates

### Manual Deployment

```bash
cd apps/homeserver
docker compose pull homeserver-fe
docker compose up -d homeserver-fe
```

## Docker Configuration

- **Base image:** Multi-stage — `node:22-alpine` (build) → `nginx:alpine` (serve)
- **Port:** `80` (internal, not exposed to host — behind Nginx reverse proxy)
- **Resource limits:** 64MB RAM, 0.25 CPU
- **Security:** read-only filesystem, no-new-privileges, tmpfs for Nginx cache/run/tmp

## Troubleshooting

| Issue | Cause | Solution |
|-------|-------|----------|
| Status dots stuck red / vitals show `...` | `PUBLIC_API_BASE_URL` unreachable from the browser, or blocked by CORS | Verify `be-homeserver` is healthy and reachable at the configured URL; check the browser console for fetch errors |
| Stats don't populate for a card | `data-container` / `data-dot-for` value doesn't match a container name | These must match (or be a substring of) the container name reported by `be-homeserver`'s `/docker` endpoint |
| Env var changes have no effect after redeploy | `BASE_DOMAIN` / `PUBLIC_API_BASE_URL` are baked in at **build time** | Rebuild and push a new image — restarting the running container alone won't pick up new values |
| Blank page or 404 after deploy | Static build missing, or Nginx serving the wrong directory | Check `docker logs homeserver-fe` and confirm the multi-stage build completed and copied `dist/` into the Nginx image |

## Project Structure

```
fe-homeserver/
├── .env.example        # Environment variable template
├── .github/
│   └── workflows/
│       └── deploy.yml  # CI/CD — build & push to GHCR
├── .gitignore
├── Dockerfile          # Multi-stage: Astro build → Nginx serve
├── nginx.conf          # Custom Nginx config: gzip + custom 404 routing
├── astro.config.mjs    # Astro configuration (compressHTML: true)
├── docs/               # Documentation (this directory)
├── package.json
├── public/             # Static assets (favicon.svg)
├── src/
│   ├── components/     # Reusable Astro components
│   │   ├── Card.astro          # Container/app card slot (div or <a>)
│   │   ├── HeaderControls.astro # Toggle + polling controls bar
│   │   ├── HistoryCharts.astro  # SVG analytics chart panel
│   │   └── VitalsBar.astro      # Host CPU/RAM/Disk/Temp bar
│   └── pages/
│       ├── index.astro  # Main dashboard page
│       └── 404.astro    # Custom branded error page
└── tsconfig.json       # TypeScript configuration
```
