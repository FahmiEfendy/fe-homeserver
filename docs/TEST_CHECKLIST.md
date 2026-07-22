# Test Checklist — Homeserver Frontend (`fe-homeserver`)

Manual test procedures for validating the frontend dashboard.

---

## Static Site Serving

- [ ] Nginx serves the Astro build at `/` without errors
- [ ] `index.html` is loaded as the default page
- [ ] Static assets (CSS, JS, images) load correctly
- [ ] Returns `200` for the root path
- [ ] Returns custom branded `404.html` for unknown routes (not default Nginx 404)
- [ ] Response headers include proper `Content-Type` for all assets
- [ ] Gzip compression is active (check `Content-Encoding: gzip` response header)

---

## Theme Toggle

- [ ] Dark mode is applied by default on fresh visit
- [ ] Light mode activates when system preference is `prefers-color-scheme: light` (no saved theme)
- [ ] Theme toggle button switches between dark and light mode
- [ ] Selected theme persists after page refresh (stored in `localStorage`)
- [ ] No flash of unstyled/incorrect theme on page load (inline `<script>` in `<head>` fires first)
- [ ] Historical chart gradient opacity adjusts correctly between themes
- [ ] Sun icon visible in light mode; moon icon visible in dark mode

---

## API Connectivity

- [ ] Dashboard successfully fetches from `PUBLIC_API_BASE_URL/vitals`
- [ ] Dashboard successfully fetches from `PUBLIC_API_BASE_URL/docker`
- [ ] Dashboard handles API errors gracefully (shows red error banner, not blank page)
- [ ] Dashboard handles Docker socket errors (shows yellow docker daemon alert)
- [ ] Dashboard handles API timeout gracefully
- [ ] No CORS errors in browser console

---

## System Vitals Display

- [ ] CPU load percentage is displayed and updates on each poll
- [ ] RAM usage (used / total in GB) is displayed correctly
- [ ] Disk usage percentage is displayed
- [ ] Temperature is displayed (or skeleton until available)
- [ ] Skeleton shimmers are visible before first data fetch
- [ ] Skeletons are removed after successful fetch
- [ ] Disk progress bar fills to correct percentage in expanded view

---

## Historical Analytics Charts

- [ ] "Show Vitals Charts" toggle reveals the expanded vitals view and the analytics card
- [ ] Shimmer placeholder is visible until at least 2 data points are collected
- [ ] CPU history tab shows green area chart
- [ ] RAM history tab shows blue area chart
- [ ] Temperature history tab shows orange area chart
- [ ] Y-axis scale labels are correct for each tab
- [ ] SVG chart lines do not overflow the chart bounds
- [ ] Switching tabs re-renders the chart without artifacts

---

## Docker Container Display

- [ ] All 19 monitored containers are listed
- [ ] Each container shows: CPU%, memory%, memory usage, uptime/status
- [ ] Healthy containers show green status dot
- [ ] Unhealthy/stopped containers show red status dot
- [ ] "Show Container Stats" toggle reveals/hides stats panel with animation
- [ ] Container sparklines (CPU, memory) render and update correctly

---

## Auto-Refresh Polling

- [ ] Polling starts automatically at 10s interval on page load
- [ ] Interval selector changes polling frequency correctly (5s, 10s, 30s, 60s)
- [ ] "Paused" option stops polling and shows "Polling paused" status
- [ ] Manual refresh button triggers an immediate fetch
- [ ] Refresh icon spins during data fetch
- [ ] Sync status displays "Synced at HH:MM:SS" on success
- [ ] Sync status displays "Sync error at HH:MM:SS" in red on failure

---

## Responsive Design

- [ ] Dashboard renders correctly on desktop (1920×1080)
- [ ] Dashboard renders correctly on tablet (768×1024)
- [ ] Dashboard renders correctly on mobile (375×667)
- [ ] Header controls wrap cleanly at < 600px
- [ ] Expanded vitals show 2-column layout at < 600px
- [ ] Container stats grid shows 2×2 at < 480px
- [ ] No horizontal scrolling on any viewport
- [ ] Touch interactions work on mobile

---

## Environment Variables (Build-Time)

- [ ] `BASE_DOMAIN` is correctly embedded — all external app links use the right domain
- [ ] `PUBLIC_API_BASE_URL` is correctly embedded — API calls reach the correct host
- [ ] Changing env vars requires a new image build (no runtime override)

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
