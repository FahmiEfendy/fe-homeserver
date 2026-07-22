# TODO — Homeserver Frontend (`fe-homeserver`)

Planned improvements and future work items.

---

## 🔴 Critical


## 🟡 Medium

- [ ] **Container filtering** — Add filters by type (database, app, infra, monitoring, media)
- [ ] **Container search** — Search containers by name
- [ ] **Container sorting** — Sort by name, CPU, memory, status
- [ ] **Notification badges** — Highlight unhealthy or stopped containers with badge counts

## 🟢 Nice to Have

- [ ] **Container detail view** — Click a container to see detailed stats and recent logs
- [ ] **Quick actions** — Restart/stop containers from the dashboard (requires backend write API)
- [ ] **Multi-server support** — Monitor multiple homeservers from a single dashboard
- [ ] **PWA support** — Add service worker for offline access and push notifications
- [ ] **Keyboard shortcuts** — Navigate dashboard with keyboard (refresh, filter, search)
- [ ] **Accessibility audit** — Ensure WCAG 2.1 AA compliance
- [ ] **i18n support** — Internationalization for multiple languages

## Tech Debt

- [ ] **Move API URL to runtime config** — Avoid requiring image rebuild when API URL changes (consider Nginx env substitution or runtime JS config)
- [ ] **Add component library** — Extract reusable UI components (cards, badges, stat widgets)
- [ ] **Add E2E tests** — Playwright or Cypress tests for critical dashboard flows
- [ ] **Optimize build size** — Audit and minimize bundle size
- [ ] **Custom 404 page** — Replace default Nginx 404 with branded page
