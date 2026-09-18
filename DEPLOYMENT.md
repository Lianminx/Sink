# Lianmin Sink deployment

- Dashboard: https://lianmin-links.uptimeworker.workers.dev/dashboard
- Example link: https://lianmin-links.uptimeworker.workers.dev/blog
- Personal configuration: `wrangler.personal.jsonc` (use this instead of the upstream deployment configuration).
- Storage: D1 `lianmin-sink` and KV `lianmin-sink`.
- Free-only deployment: no paid plan, custom domain, R2, Workers AI, or backup cron. Analytics Engine uses dataset `lianmin_sink` through binding `ANALYTICS`.
- Link management, redirects and local JSON export are available. Analytics queries are enabled; AI suggestions, image uploads and R2 backups are not configured.

## Authentication

The administrator token is stored as the Worker secret `NUXT_SITE_TOKEN`. Its local copy is in `.dev.secrets.json`, which is ignored by Git. Never commit or publish it. Enter this token in the dashboard login form. Cloudflare credentials are managed separately by Wrangler.

## Build and publish

Use Node.js 24+ and pnpm 11.11.0. On Windows, set `NODE_OPTIONS=--max-old-space-size=8192` in PowerShell, then run:

```powershell
pnpm install --frozen-lockfile
pnpm build:sphere
pnpm exec nuxt build
pnpm exec wrangler d1 migrations apply DB --remote --config wrangler.personal.jsonc
pnpm exec wrangler deploy --config wrangler.personal.jsonc
```

For a new instance, complete `/api/link/migration/run` with authenticated POST before creating links, even when KV is empty. The deployed instance completed this initialization on 2026-09-17.

Commit and push changes to the personal fork. GitHub pushes do not automatically deploy. Keep upstream workflows disabled unless explicitly configured for this instance.

## Verification

On 2026-09-17: dashboard returned 200, authenticated verification returned 200, an unauthenticated API call returned 401, and `/blog` redirected to the blog with HTTP 301. These requests succeeded through the local proxy; direct workers.dev access from this machine failed. Browser usability still depends on the user's network.

## Analytics (2026-09-18)

The query credential is stored only as Worker secret `NUXT_CF_API_TOKEN`. Account and dataset are configured in `wrangler.personal.jsonc`. The supplied token was verified by a successful Analytics Engine SQL request; its full permission scope was not audited. Future tokens need Account Analytics Read for this account.

Verification: authenticated `/api/stats/counters?slug=blog` returned a real record with visits=1; the public short link returned HTTP 301. Browser charts have not been visually tested. Earlier unrecorded clicks cannot be recovered, ingestion can be delayed, and browser-cached redirects may skip the Worker. No paid plan was enabled.
