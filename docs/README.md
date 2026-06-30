# Homeserver Frontend (`fe-homeserver`)

> Astro-based static dashboard for monitoring the homeserver — served via Nginx.

## Overview

A lightweight Astro static site that serves as the homeserver dashboard. It provides a visual interface for monitoring system vitals (CPU, RAM, disk, temperature) and Docker container statuses by consuming the backend API (`be-homeserver`).

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

## Project Structure

```
fe-homeserver/
├── .env.example        # Environment variable template
├── .github/
│   └── workflows/
│       └── deploy.yml  # CI/CD — build & push to GHCR
├── .gitignore
├── Dockerfile          # Multi-stage: Astro build → Nginx serve
├── astro.config.mjs    # Astro configuration
├── docs/               # Documentation (this directory)
├── package.json
├── public/             # Static assets (favicon, images)
├── src/
│   ├── layouts/        # Page layout templates
│   └── pages/          # Route pages (file-based routing)
└── tsconfig.json       # TypeScript configuration
```
