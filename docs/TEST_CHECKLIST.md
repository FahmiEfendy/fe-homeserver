# Test Checklist — Homeserver Frontend (`fe-homeserver`)

Manual test procedures for validating the frontend dashboard.

---

## Static Site Serving

- [ ] Nginx serves the Astro build at `/` without errors
- [ ] `index.html` is loaded as the default page
- [ ] Static assets (CSS, JS, images) load correctly
- [ ] Returns `200` for the root path
- [ ] Returns `404` page for unknown routes
- [ ] Response headers include proper `Content-Type` for all assets

---

## API Connectivity

- [ ] Dashboard successfully fetches from `PUBLIC_API_BASE_URL/vitals`
- [ ] Dashboard successfully fetches from `PUBLIC_API_BASE_URL/docker`
- [ ] Dashboard handles API errors gracefully (shows fallback UI, not blank page)
- [ ] Dashboard handles API timeout gracefully
- [ ] No CORS errors in browser console

---

## System Vitals Display

- [ ] CPU load percentage is displayed and updates
- [ ] RAM usage (used / total) is displayed correctly
- [ ] Disk usage percentage is displayed
- [ ] Temperature is displayed (or "N/A" when unavailable)
- [ ] Values refresh without full page reload

---

## Docker Container Display

- [ ] All 19 monitored containers are listed
- [ ] Each container shows: name, CPU%, memory, network I/O, status
- [ ] Healthy containers show green/positive indicator
- [ ] Unhealthy containers show red/warning indicator
- [ ] Stopped containers are visually distinct
- [ ] Git branch is displayed for public-facing app containers
- [ ] Containers without git info do not show branch field

---

## Environment Variables (Build-Time)

- [ ] `BASE_DOMAIN` is correctly embedded in built output
- [ ] `PUBLIC_API_BASE_URL` is correctly embedded in built output
- [ ] Links to other apps use the correct domain
- [ ] Changing env vars requires a new image build (no runtime override)

---

## Responsive Design

- [ ] Dashboard renders correctly on desktop (1920×1080)
- [ ] Dashboard renders correctly on tablet (768×1024)
- [ ] Dashboard renders correctly on mobile (375×667)
- [ ] No horizontal scrolling on any viewport
- [ ] Touch interactions work on mobile

---

## Docker Health Check

- [ ] Docker health check passes after `start_period` (15s)
- [ ] Container status shows `(healthy)` after startup
- [ ] Container auto-restarts on crash (`unless-stopped`)

---

## Security & Hardening

- [ ] Container runs with `read_only: true` filesystem
- [ ] `no-new-privileges` security option is active
- [ ] Container respects memory limit (64MB)
- [ ] Container respects CPU limit (0.25)
- [ ] No ports exposed to host (only accessible via proxy network)
- [ ] Nginx tmpfs directories are correctly mounted

---

## Build Pipeline

- [ ] GitHub Actions workflow triggers on push to `main`
- [ ] Docker image builds successfully with build args
- [ ] Image is pushed to GHCR with `latest` and SHA tags
- [ ] Watchtower detects and pulls the updated image
